//nikonikoRSSから新着を取得して１日以上経った動画の再生数やらブクマを再取得
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>ライブドアの天気予報を表示する</title>
</head>
 <body>
<?php

$filename = 'test.txt';
$filename = 'sst.txt';
$filename = 'gyousuu.txt';
$filename2 = 'RSSlog.txt';
$i = 0;//総検索済み回数
$v = 0;// function Filewriteでつかう存在する動画の通し番号(0,1,2,3,4,5・・・・)
$j = 0;// function Filewrite存在する動画の通し番号(0,1,2,3,4,5・・・・)
  $p=1;

$size= 100;//動画取得数

  if(  isset($_GET['page'])  ){  //サニタイズ 数値なら代入、違えば1
    if( is_numeric($_GET['page']) ){
      $p=$_GET['page'];
    }
  }

function OnedaySerchSQL(){
global $x,$v,$size,$p;// 通し番号を付けとく
global $tousi;
$v+=1;
$nowstamp=time();
date_default_timezone_set('Asia/Tokyo');
$time=date( "Y年m月d日 H時i分s秒" ) ;
$link = mysql_connect('localhost', 'root', '');
if (!$link) {
    die('接続失敗です。'.mysql_error());
}

print('<p>接続に成功しました。</p>');

$db_selected = mysql_select_db('nikonikodouga', $link);
if (!$db_selected){
    die('データベース選択失敗です。'.mysql_error());
}

print('<p>uriageデータベースを選択しました。</p>');

mysql_set_charset('utf8');

$result = mysql_query('SELECT * FROM rss');
if (!$result) {
    die('クエリーが失敗しました。'.mysql_error());
}

//今取得したタイムスタンプとrss取得時のタイムｓタンプの差が86400秒以上(1日経過)なら再取得する(１分は６０秒。１時間に1分は60回あるから60×60で3600秒。１日で１時間は24回あるから24×3600で86400)
$sql = "SELECT * FROM rss limit " . $p*10 . ", 10 ";//10件ずつ表示する。次ページ押すとGETから変数pに+１される
$result_flag = mysql_query($sql);
$i = 0;
//↓の取得件数は上のSELECT文でマッチした件数だけループするみたい。変わる
while ($row = mysql_fetch_assoc($result_flag)) {//上のタイトルが空白のやつはデータから消したあとのデータベースを確認のため全件表示する
    if($i>=$size)break;
    print('<p>');
    print('title='.$row['title']);
    print(',syutokuzumi='.$row['syutokuzumi']);
    print(',syutokuTIME='.$row['syutokuTIME']);
    print(',smtsuzuki='.$row['smtsuzuki']);
    print('</p>');
   $x[$i]->URL   = $row['smtsuzuki'];
   $x[$i]->RSSsyutokuTime   = $row['syutokuTIME'];
   $x[$i]->RSStimestamp   = $row['timestamp'];
$i++;
}
$close_flag = mysql_close($link);
if ($close_flag){
    print('<p>切断に成功しました。</p>');
} return $i;
}





//クラスで構造体を作る。public変数は個人の名前・年齢・体重みたいなもん
class A
{
  public $num;
  public $sm;
  public $imgurl;
  public $tltle;
  public $setsumei;
  public $view;
  public $mylist;
  public $coment;
  public $URL;
  public $tag;
  public $RSSsyutokuTime;
  public $RSStimestamp;

}
 
//ここが構造体の宣言。左辺に要素数書くと構造体の配列になる.c言語ならこれで構造体の配列になるが
//phpの場合、配列すべてに、個別にnewを宣言しないとメモリが割り当てられないため
//forループを使ってすべてにnew宣言している。以下で要素数150の構造体の配列ができた
//とりあえずXmlは150件までしか取得できないことにする
for($i=0 ; $i<=$size; $i++) {
$x[$i] = new A;
}


 OnedaySerchSQL();

 ?>


<a href="?page=<?php echo $p+1; ?>">次へ</a>
<a href="?page=<?php echo $p+1; ?>">1</a>
<a href="?page=<?php echo $p+2; ?>">2</a>
<a href="?page=<?php echo $p+3; ?>">3</a>
<a href="?page=<?php echo $p+4; ?>">4</a>
<a href="?page=<?php echo $p+5; ?>">5</a>
<?php
$page = $_REQUEST["page"];
$ketsu=$page-5;

  for($i=$ketsu ; $i<6; $i++){
echo "<a href=?page=", $i, ">6</a>";
 }

?>

</body>
</html> 
