---
layout: post
title: "打开文件读取日志里面的id等信息；"
date: 2015-11-09 03:48:00.000000000 +09:00
tags: ""
---
    $fp = fopen(''msg.log-2015-11-08'', "r");
            $data =array();
            while(!feof($fp)){
                $a=fgets($fp);
                if(strpos($a, ''serverId'') !== false){
                	$reg = "#(?<=\\[).*?(?=\\])#";
                	preg_match_all($reg, $a, $result);
                	// $data[]=$result;
                	$serverType=explode(''_'', trim($result[0][3]));
                	if ($serverType[0]!=="iOS") {
                		$serverId=$serverType[0];
                	}else{
                		$serverId=''iOS_''.$serverType[1];
                	}
                	$data[]=array(''playerId''=>trim($result[0][2]),''serverId''=>$serverId);
                }
            }
        fclose ($fp);
       var_dump($data);