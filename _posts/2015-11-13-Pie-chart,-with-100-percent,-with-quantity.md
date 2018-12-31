---
layout: post
title: "饼图， 带百分百，带数量"
date: 2015-11-13 10:46:00.000000000 +09:00
---

![7.png](https://o8ekw8sx0.qnssl.com/upload/201511/T_1vtU7TPtYAqpvRVmcZD01He-V18N8H.png "7.png")

    <?php
    /**
     * @author Yaodong Zhao <yaodong.zhao@gree.co.jp>
     * @date 2013-04-21
     * @copyright All Copyright (c) GREE China, Inc. (http://corp.gree.net/cn/)
     */
    Yii::import(''application.widgets.HighChartsWidget'');
    class FightDeviceChartWidget extends HighChartsWidget
    {
        /**
         * @var CActiveDataProvider
         */
        public $dataProvider = null;
    
        /**
         * @var string object attribute name of counter
         */
        public $attributeDate = ''stat_date'';
    
        /**
         * @var string object attribute name of counter
         */
        public $attributeCounter = '''';
    
        /**
         * @var string
         */
        public $counterFormatter = ''int'';
    
        protected function getConfig()
        {
    
            $config = array(
                ''chart''=>array(
                    ''plotBackgroundColor''=>null,
                    ''plotBorderWidth''=>null,
                    ''plotShadow''=>false,
                    ''type''=>''pie''
                ),
                ''title'' => array(
                    ''text''  => $this->title,
                ),
                ''subtitle'' => array(
                    ''text''  => $this->description,
                ),
                ''tooltip''=>array(
                    ''pointFormat'' => ''{series.name}: <b>{point.y}</b>''
                ),
                ''plotOptions''=>array(
                    ''pie''=>array(
                        ''allowPointSelect''=>true,
                        ''cursor''=>''pointer'',
                        ''dataLabels''=>array(
                            ''enabled''=>true,
                            ''format''=> ''<b>{point.name}</b>: {point.percentage:.1f} %''
                        ),
    //                    ''showInLegend''=>true
                    )
                ),
                ''series'' => array(
                    array(
                        ''name'' => ''Brands'',
                        ''colorByPoint'' => true,
                        ''data'' => array(
                            array(
                                ''name''=> "Microsoft Internet Explorer",
                                ''y''=> 56.33
                            ),array(
                                ''name''=> "Chrome",
                                ''y''=> 24.03
                            ),array(
                                ''name''=> "Firefox",
                                ''y''=> 10.38
                            ), array(
                                ''name''=> "Safari",
                                ''y''=> 4.77
                            ),array(
                                ''name''=> "Opera",
                                ''y''=> 0.91
                            ),array(
                                ''name''=> "Proprietar or Undetectable",
                                ''y''=> 0.2
                            )
                        )
                    )
                )
            );
            $deviceType=array(
                ''iPhone3,1'' => ''iPhone4'',
                ''iPhone3,2'' => ''iPhone4'',
                ''iPhone3,3'' => ''iPhone4'',
                ''iPhone4,1'' => ''iPhone4S'',
                ''iPhone5,1'' => ''iPhone5'',
                ''iPhone5,2'' => ''iPhone5'',
                ''iPhone5,3'' => ''iPhone5c'',
                ''iPhone5,4'' => ''iPhone5c'',
                ''iPhone6,1'' => ''iPhone5s'',
                ''iPhone6,2'' => ''iPhone5s'',
                ''iPhone7,1'' => ''iPhone6'',
                ''iPhone7,2'' => ''iPhone6 plus'',
                ''iPhone8,1'' => ''iPhone6s'',
                ''iPhone8,2'' => ''iPhone6s plus'',
                ''iPod4,1''   => ''iPod touch4'',
                ''iPod5,1''   => ''iPod touch5'',
                ''iPad3,1'' => ''iPad 3'',
                ''iPad3,2'' => ''iPad 3'',
                ''iPad3,3'' => ''iPad 3'',
                ''iPad2,1'' => ''iPad2'',
                ''iPad2,2'' => ''iPad2'',
                ''iPad2,3'' => ''iPad2'',
                ''iPad2,4'' => ''iPad2'',
                ''iPad2,5'' => ''iPad mini'',
                ''iPad2,6'' => ''iPad mini'',
                ''iPad2,7'' => ''iPad mini'',
                ''iPad3,4'' => ''iPad 4'',
                ''iPad3,5'' => ''iPad 4'',
                ''iPad3,6'' => ''iPad 4'',
                ''iPad4,1'' => ''Ipad air'',
                ''iPad4,2'' => ''Ipad air'',
                ''iPad4,3'' => ''iPadmini2'',
                ''iPad4,4'' => ''iPadmini2'',
                ''iPad4,5'' => ''iPadmini2'',
                ''iPad4,6'' => ''iPadmini3'',
                ''iPad4,7'' => ''iPadmini3'',
                ''iPad5,1'' => ''Ipad air2'',
                ''iPad5,2'' => ''Ipad air2'',
                ''iPad5,3'' => ''Ipad air2'',
            );
            $data = $this->dataProvider->getData();
    //        print_r($data);die;
            foreach ($data as $r) {
                if($this->attributeCounter==''device''&&$r->device){
                    if($r->device==''''||$r->device==''device''||$r->device==''???'') {
                        $date[] = ''模拟器'';
                    }else{
                        $date[] =$deviceType[$r->device];
                    }
                }
                if($this->attributeCounter==''system''&&$r->device){
                    if($r->system==''system''||$r->system==''???''||$r->system=='''') {
                        $date[] = ''模拟器'';
                    }else{
                        $date[] =$r->system;
                    }
                }
    //           print_r($date);die;
            }
    //        print_r($date);
    //        if(isset($date)) {
            sort($date);
            $count = count($date);
    //        $config [''tooltip''][''pointFormat'']= ''{series.name}:''."''{point.percentage}''*$count";
            $deviceData = array_count_values($date);
            $series = array(
                ''name'' => ''num'',
            );
            foreach ($deviceData as $k => $v) {
                $series[''data''][] = array(
                    ''name'' => $k,
                    ''y'' => $v
                );
            }
    
            $config[''series''][0] = $series;
    //        print_r($config);die;
    //        $config[''xAxis''][''categories''] = array_keys($rawData);
    //        }
            return $config;
        }
    
    
    }