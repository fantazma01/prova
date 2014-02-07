<?php
 
/*
#########################################################
#       Copyright to Zatarra @ rstforums.com            #
#        Use it only with your own accounts!            #
#########################################################
*/
 
//The link variable is the only one you have to modify!!! Leave the rest as it is in order to work.
$link='https://www.facebook.com/photo.php?v=312906458876641&comment_id=6489767&offset=0&total_comments=575';
 
@preg_match_all('/sts\/([0-9]*?)\?comment/',$link,$postid);
if (!@$postid[1][0])
{
@preg_match_all('/fbid=([0-9]*?)\&set/',$link,$postid);
}
@preg_match_all("/comment_id=([0-9]*?)\&offset/",$link,$commentid);
@preg_match_all("/a.([0-9]*?).([0-9]*?).([0-9]*?)\&/",$link,$photo);
if (@$photo[2][0])
{$length=strlen($photo[2][0]);}
else
{$length=5;}
 
//Initializing cookies folder
$directory=getcwd()."/cookies";
 
//Checking if cookies folder exists
if (file_exists($directory))
{
//Eating all the cookies to avoid problems
echo shell_exec("rm -rf ./cookies/*.cookie");
}
else
{
//Creating directory
echo shell_exec("mkdir cookies");
}
 
//Initializing users and passwords
$user=file('users.txt');
 
function login($username,$password)
{
$curl = curl_init();
curl_setopt($curl, CURLOPT_URL, "https://login.facebook.com/login.php?m&next=http://m.facebook.com/home.php");
curl_setopt($curl, CURLOPT_FOLLOWLOCATION, 1);
curl_setopt($curl, CURLOPT_USERAGENT, "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:19.0) Gecko/20100101 Firefox/19.0");
curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($curl, CURLOPT_POST, 1);
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($curl, CURLOPT_POSTFIELDS, "email=" . @$username . "&pass=" . @$password . "&login=Log In");
curl_setopt($curl, CURLOPT_ENCODING, "");
curl_setopt($curl, CURLOPT_COOKIEJAR, "cookies/$username.cookie");
curl_setopt($curl, CURLOPT_COOKIEFILE, "cookies/$username.cookie");
$curlData = curl_exec($curl);
$curlData = curl_exec($curl);
 
@preg_match_all('/name=\"fb_dtsg\" value=\"(.*?)\"/',$curlData,$dtsg);
//echo "dtsg => ".$dtsg[1][0]."\n";
@preg_match_all('/\"id\":\"(.*?)\"\}\,/',$curlData,$userid);
//echo "Profile Id => ".$userid[1][0]."\n";
@preg_match_all('/data-sigil=(.*?)\>\<table/',$curlData,$rasp);
if (@$rasp[1][0])
{
return array(0,0);
}
else
{
return array($dtsg[1][0],$userid[1][0]);
}
}
 
function likecomment($username,$password,$fpostid,$fcommentid,$flength)
{
$userdetails=login($username,$password);
 
if ($userdetails[0]!='0')
{
if ($flength==5)
{
$data='comment_id='.$fpostid.'_'.$fcommentid.'&legacy_id='.$fcommentid.'&like_action=true&ft_ent_identifier='.$fpostid.'&source=2&client_id=1375725162708%3A3550641995&ft[tn]=%3ER0]&__user='.$userdetails[1].'&__a=1&__dyn=7n8ahyj35CFUSt2u5FeDKd8q&__req=q&fb_dtsg='.$userdetails[0].'&ttstamp=26581665371788481';
}
else
{
$data='comment_id='.$fpostid.'_'.$fcommentid.'&legacy_id='.$fcommentid.'&like_action=true&ft_ent_identifier='.$fpostid.'&source=2&client_id=1375725162708%3A3550641995&ft[tn]=%3ER0]&ft[type]=44&nctr[_mod]=photos_snowlift&__user='.$userdetails[1].'&__a=1&__dyn=7n8ahyj35CFUSt2u5FeDKd8q&__req=s&fb_dtsg='.$userdetails[0].'&ttstamp=26581665371788481';
}
$curl = curl_init();
curl_setopt($curl, CURLOPT_URL, "https://www.facebook.com/ajax/ufi/comment_like.php");
curl_setopt($curl, CURLOPT_FOLLOWLOCATION, 1);
curl_setopt($curl, CURLOPT_USERAGENT, "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:19.0) Gecko/20100101 Firefox/19.0");
curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($curl, CURLOPT_POST, 1);
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
curl_setopt($curl, CURLOPT_ENCODING, "");
curl_setopt($curl, CURLOPT_COOKIEFILE, "cookies/$username.cookie");
$curlData = curl_exec($curl);
$curlData = curl_exec($curl);
echo $curlData;
}
}
 
for ($i=0;$i<count($user);$i++)
{
$fb=explode(" ",trim($user[$i]));
likecomment($fb[0],$fb[1],@$postid[1][0],@$commentid[1][0],$length);
likecomment($fb[0],$fb[1],@$postid[1][0],@$commentid[1][0],$length);
}
 
//Eating all the remain cookies
echo shell_exec("rm -rf cookies/*.cookie");
 
?>
