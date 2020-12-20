**一、基本功能实现**

1. 显示时间戳：

   NotePad的database中有“modify_time”，使用modify_time作为时间戳，显示在标题下方。

   在notepad.java中有代码块

   ```
   /**
    * Column name for the modification timestamp
    * <P>Type: INTEGER (long from System.curentTimeMillis())</P>
    */
   public static final String COLUMN_NAME_MODIFICATION_DATE = "modified";
   ```

   第一步：在notelist_time.xml中新建用来显示modifytime

   ```
       <TextView
           android:id="@+id/text2"
           android:layout_width="433dp"
           android:layout_height="match_parent"
           android:layout_margin="0dp"
           android:layout_weight="1"
           android:gravity="center_vertical"
           android:paddingLeft="10dip"
           android:textAppearance="?android:attr/textAppearanceLarge"
           android:textSize="12dp" />
   ```

   第二步：在NoteList.java的 PROJECTION 中加入修改时间条NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE

   应用显示笔记时，获取 PROJECTION 中设定的值。

   ```
       private static final String[] PROJECTION = new String[] {
               NotePad.Notes._ID, // 0
               NotePad.Notes.COLUMN_NAME_TITLE, // 1
               NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,
       };
   ```

   ```
   private String[] dataColumns = { 
   				NotePad.Notes.COLUMN_NAME_TITLE,
   				NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE
   				} ;
   ```

   第三步：修改时间戳的格式，原格式是 Long 型数据：在 NoteEditor.java 中的updateNote() 中设置，获取当前时间

   ```
   SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm");
   String format = sf.format(d);
   values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, format);
   ```

   运行效果：

   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/modifytime1.png)
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/modifytime2.png)

2. 搜索功能的实现

   第一步：在响应的xml文件中修改，提供搜索文本框

   ```
           <android.support.v7.widget.SearchView
               android:id="@+id/search"
               android:layout_width="match_parent"
               android:layout_height="78dp"
               >
   ```

   第二步：在notelist中添加搜索方法

   ```
   private void SearchView(){
           searchView=findViewById(R.id.sv);
           searchView.onActionViewExpanded();
           searchView.setQueryHint("搜索");
           searchView.setSubmitButtonEnabled(true);
           searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
               @Override
               public boolean onQueryTextSubmit(String s) {
                   return false;
               }
               @Override
               public boolean onQueryTextChange(String s) {
                   if(!s.equals("")){
                       String selection=NotePad.Notes.COLUMN_NAME_TITLE+" GLOB '*"+s+"*'";
                       updatecursor = getContentResolver().query(
                               getIntent().getData(),          
                               PROJECTION,                    
                               selection,                           
                               null,                            
                               NotePad.Notes.DEFAULT_SORT_ORDER  
                       );
                       if(updatecursor.moveToNext())
                           Log.i("daawdwad",selection);
                   }
                  else {
                       updatecursor = getContentResolver().query(
                               getIntent().getData(),           
                               PROJECTION,                      
                               null,                        
                               null,                           
                               NotePad.Notes.DEFAULT_SORT_ORDER
                       );
                   }
                   adapter.swapCursor(updatecursor);
                   return false;
               }
           });
       }
   ```

   第三步：在 AndroidManifest 中为 注册

   ```
   <application>
   	<activity android:name=".NoteSearch" android:label="@string/search_note"/>
   </application>
   ```

   运行效果：
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/search1.png)
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/search2.png)

   

   3.实现ui美化及各种小图标的替换

   第一步：在菜单的布局文件（xml文件）中创建菜单栏

   ```
   <item
           android:title="改变颜色">
           <menu>
               <item
                   android:title="改变背景颜色"
                   android:id="@+id/background-color">
               </item>
               <item android:id="@+id/text-color"
                   android:title="改变字体颜色">
               </item>
           </menu>
       </item>
   ```

   第二步：修改颜色调用的是函数showColor()，在NoteEditor增加点击事件

   ```
     private void showColor(){
           AlertDialog alertDialog=new AlertDialog.Builder(this).setTitle("请选择颜色").
                  setView(R.layout.color_layout)
                   .setPositiveButton("确定", new DialogInterface.OnClickListener() {
                       @Override
                       public void onClick(DialogInterface dialog, int which) {
                           dialog.dismiss();
                       }
                   }).create();
           alertDialog.show();
       }
   ```

   ```
    public void onClick(View v) {
           switch (v.getId()){
               case R.id.aqua:
                   if(isFlag){
                       mText.setBackgroundColor(Color.parseColor("#00FFFF"));
                       colorBack="#00FFFF";
                   }else{
                       mText.setTextColor(Color.parseColor("#00FFFF"));
                       colorText="#00FFFF";
                   }
                   break;
                   ········
           }
       }
   ```

   运行效果显示：
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/ui1.png)
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/ui2.png)
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/colormenu1.png)
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/colorchose.png)
   [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/color3.png)
   

   4.实现插入图片的功能

   第一步：在菜单的布局文件（xml文件）中创建插入图片的按钮

   ```
   <item android:title="插入图片"
           android:icon="@drawable/ic_menu_gallery"
           app:showAsAction="always|withText">
           <menu>
               <item
                   android:title="相册"
                   android:icon="@drawable/ic_menu_gallery"
                   android:id="@+id/insert_album">
               </item>
           </menu>
       </item>
   ```

   第二步：拍照要在AndroidManifest.xml中设置权限

   ```
   <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
   <uses-permission android:name="android.permission.CAMERA" />
   <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
   ```

   ```
   //从相册取图片
   public void getPhoto() {
           Intent intent = new Intent();
           intent.setType("image/*");
           intent.setAction(Intent.ACTION_OPEN_DOCUMENT);
           startActivityForResult(intent, PHOTO_FROM_GALLERY);
   }
   ```

   第三步：定义两个正则表达式，提取文本的图片路径，用SpannableString进行图片文字替换，从而实现图片的插入

   ```
   private static final String regex="content://com.android.providers.media.documents/"
       +"document/image%\\w{4}";
   private static final String reg="file:///storage/emulated/0/\\d+.jpg";
   ```

   第四步：编写onActivityResult定义返回动作

   ```
       @Override
       protected void onActivityResult(int requestCode, int resultCode, Intent data) {
           ContentResolver resolver = getContentResolver();
           super.onActivityResult(requestCode, resultCode, data);
           //第一层switch
           switch (requestCode) {
               case PHOTO_FROM_GALLERY:
                   //第二层switch
                   switch (resultCode) {
                       case RESULT_OK:
                           if (data != null) {
                               Uri uri = data.getData();//获取Intent uri
                               Bitmap bitmap = null;
                               //判断该路径是否存在
                               try {
                                   Bitmap originalBitmap = BitmapFactory.decodeStream(resolver.openInputStream(uri));
                                   bitmap = resizeImage(originalBitmap, 200, 200);//图片缩放
                               } catch (FileNotFoundException e) {
                                   e.printStackTrace();
                               }
                               if(bitmap != null){//如果图片存在
                                   //将选择的图片追加到EditText中光标所在位置
                                   int index = mText.getSelectionStart(); //获取光标所在位置
                                   Editable edit_text = mText.getEditableText();//编辑文本框
                                   if(index <0 || index >= edit_text.length()){
                                       edit_text.append(uri.toString());
                                       updateNoteText(mText.getText().toString());//更新到数据库
                                   }else{
                                       edit_text.insert(index,uri.toString());
                                       updateNoteText(mText.getText().toString());//更新到数据库
                                   }
                               }else{
                                   Toast.makeText(NoteEditor.this, "获取图片失败", Toast.LENGTH_SHORT).show();
                               }
                           }
                           break;
                       case RESULT_CANCELED:
                           break;
                   }
                   break;
       }
   ```

   第五步：onResumn（）和cancelNote（）中编写

   ```
       int colNoteIndex = mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_NOTE);//提取数据库中的文本
   
       String note = mCursor.getString(colNoteIndex);
       ArrayList<String> contentList=new ArrayList<>();//存放图片路径
       ArrayList<Integer> startList=new ArrayList<>();//存放图片路径起点位置
       ArrayList<Integer> endList=new ArrayList<>();//存放图片路径终点位置
       Pattern p=Pattern.compile(regex);
       Matcher m=p.matcher(note);
       //当匹配到图库相应资源地址，将对应信息存入List
       while(m.find()){
           contentList.add(m.group());
           startList.add(m.start());
           endList.add(m.end());
           flag=true;
       }
       p=Pattern.compile(reg);
       m=p.matcher(note);
       //当匹配到拍照的图片相应资源地址，将对应信息存入List
       while(m.find()){
           contentList.add(m.group());
           startList.add(m.start());
           endList.add(m.end());
           flag=true;
       }
       //判断文本中是否有图片资源地址
       if(!flag){
           mText.setText(note);
       }else{
           pushPicture(note,contentList,startList,endList);
       }
   ```

   运行效果：
  [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/photo1.png)
  [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/photo2.png)
  [!image](https://github.com/ZiChang818/middlenotepad/blob/master/photo/photo3.png)
   

