---
layout: article
title: "node post请求"
date: 2015-12-08 09:12:00.000000000 +09:00
---

    app.post(''/chargeDiamond'', function(res) {
    var msg = "";
    res.setEncoding(''utf8'');
    // console.log(req.body);
    res.on("data", function(chunk) {
        msg += chunk;
        var modeDesc = "Player.addDiamond(diamond, cb)"
        var paramDesc = "@param cb : 前端处理回调函数" + "</br>"
                        + "@param diamond: 添加钻石数";
        var route = "connector.playerHandler.addDiamond";
        var paramList = {''diamond'': 10000};
        var json = JSON.stringify(paramList);
        setRequestDesc(modeDesc, paramDesc, route, "{}", json);
        sendRequest(route, paramList);
    });
    res.on("end", function() {
        console.log(msg.playerId);
    })});
