---
layout: post
title: "chart视图"
date: 2015-10-23 07:40:00.000000000 +09:00
---

    $config = array(
                ''chart'' => array(
                    ''zoomType''  => ''xy'',
                ),
                ''title'' => array(
                    ''text''  => $this->title,
                ),
                ''subtitle'' => array(
                    ''text''  => $this->description,
                ),
                ''xAxis'' => array(
                    ''categories'' => array()
                ),
                ''yAxis'' => array(
                    ''title'' => array(
                        ''text'' => ''Count''
                    )
                ),
                ''tooltip'' => array(
                    ''crosshairs'' => true,
                    ''shared''     => true,
                ),
                ''plotOptions'' => array(
                    ''line'' => array(
                        ''dataLabels'' => array(
                            ''enabled'' => true
                        ),
                    ),
                    ''enableMouseTracking'' => false,
                    ''column''=> array(
                        ''stacking''=> ''normal'',
                        ''dataLabels''=> array(
                            ''enabled''=> true,
                            ''color''=> ''white'',
                            ''style''=> array(
                                ''textShadow''=> ''0 0 3px black''
                            )
                        )
                    )
                ),
                ''legend''=>array(
                    ''layout'' => ''vertical'',
                    ''align'' => ''left'',
                    ''x'' => 120,
                    ''verticalAlign'' => ''top'',
                    ''y'' => 100,
                    ''floating'' => true,
                    ''backgroundColor'' => ''#FFFFFF''
                ),
                ''series'' => array(
                    array(
                        ''name'' => ''Tokyo'',
                        ''data'' => array(7.0, 6.9, 9.5, 14.5, 18.4, 21.5, 25.2, 26.5, 23.3, 18.3, 13.9, 9.6),
                    ),
                    array(
                        ''name'' => ''London'',
                        ''data'' => array(3.9, 4.2, 5.7, 8.5, 11.9, 15.2, 17.0, 16.6, 14.2, 10.3, 6.6, 4.8),
                    )
                )
            );

![QQ截图20151023153806.png](https://o8ekw8sx0.qnssl.com/upload/201510/XI4pk3WyLBcsAJumM0-bpJYYZWRYMRZU.png "QQ截图20151023153806.png")