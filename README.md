<?php
/**
 *  Author： METO
 *  Version: 0.1.0
 */

class bilibili{

    protected $_COOKIE='user_face=http%3A%2F%2Fi2.hdslb.com%2Fbfs%2Fface%2F52229f3ea9585936a4e4da9d955fbd526f404841.jpg; LIVE_LOGIN_DATA=3f73c33fc705a6d073df8042b8f9096c42236354; LIVE_LOGIN_DATA__ckMd5=33a850fa50c1b66e; bili_jct=aac8f75bdc80de37d68916b2a4cf33a5; DedeUserID=39991275; DedeUserID__ckMd5=13522cc4973e2cd1; SESSDATA=d4fa5d3d%2C1503981639%2Cf309ae62; JSESSIONID=8F573A4E87431C9932334E332BD78ECE; LIVE_BUVID=26cc38fda61b96856b38df6e60c0916c; LIVE_BUVID__ckMd5=e76b46d988e9e59e; sid=c9misd0g';
    protected $_USERAGENT='Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.101 Safari/537.36';
    protected $_REFERER='http://live.bilibili.com/';

    private function getinfo(){
        $data=json_decode(self::curl('http://live.bilibili.com/User/getUserInfo'),1);
        $a=$data['data']['user_intimacy'];
        $b=$data['data']['user_next_intimacy'];
        $per=round($a/$b*100,2);
        echo "===============================\n";
        echo "name: {$data['data']['uname']} \n";
        echo "level: {$data['data']['user_level']} \n";
        echo "exp: {$a}/{$b} {$per}%\n";
        echo "sign: ".self::sign()."\n";
        echo "===============================\n";
    }

    private function sign(){
        $raw=json_decode(self::curl('http://live.bilibili.com/sign/doSign'),1);
        return $raw['msg'];
    }

    public function cron(){
        header('Content-Type: text/txt; charset=UTF-8');
        echo date('[Y-m-d H:i:s]',time())."\n";
        $raw=json_decode(self::curl('http://live.bilibili.com/User/userOnlineHeart'),1);
        if(!isset($raw['data'][1]))echo " > SUCCESS\n";
        else echo " > INFO: already send @ ".date('Y-m-d H:i:s',$raw['data'][1])."\n";

        self::getinfo();
    }

    protected function curl($url){
        $curl=curl_init();
        curl_setopt($curl,CURLOPT_URL,$url);
        curl_setopt($curl,CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($curl,CURLOPT_CONNECTTIMEOUT, 10);
        curl_setopt($curl,CURLOPT_REFERER,$this->_REFERER);
        curl_setopt($curl,CURLOPT_COOKIE,$this->_COOKIE);
        curl_setopt($curl,CURLOPT_USERAGENT,$this->_USERAGENT);
        $result=curl_exec($curl);
        curl_close($curl);
        return $result;
    }
}

(new bilibili)->cron();
