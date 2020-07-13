---
layout: article
title: "grpc大文件传输"
date: 2020-01-10 20:39:00.000000000 +09:00
tags: golang grpc
---

>文件太大，grpc传输过程中受限于默认最大消息值4MB,怎么处理比较合适？

## 传输配置

可以调整grpc默认

## grpc流式传输

>通过使用流(streaming)，你可以向服务器或者客户端发送批量的数据， 服务器和客户端在接收这些数据的时候，可以不必等所有的消息全收到后才开始响应，而是接收到第一条消息的时候就可以及时的响应， 这显然比以前的类HTTP 1.1的方式更快的提供响应，从而提高性能。

## 不落地文件传输

``` golang
func (c *taskController) upload(ctx *gin.Context) {
    fmt.Println(time.Now().Format("2006-01-02 15:04:05"))
    s0 := time.Now()

    confFile, errGet := ctx.FormFile("file")
    if errGet != nil {
        c.error(ctx, errGet, "get file failed")
        return
    }
    f, errF := confFile.Open()
    if errF != nil {
        c.error(ctx, errF, "open file failed")
        return
    }
    defer f.Close()

    ret, err := svc.Upload(f)

    e0 := time.Now()
    d0 := e0.Sub(s0)
    fmt.Printf("time of way(0)=%v\n", d0)
}
```

``` golang
func (c *taskController) upload(ctx *gin.Context) {
    fmt.Println(time.Now().Format("2006-01-02 15:04:05"))
    s0 := time.Now()

    confFile, errGet := ctx.FormFile("file")
    if errGet != nil {
        c.error(ctx, errGet, "get file failed")
        return
    }
    f, errF := confFile.Open()
    if errF != nil {
        c.error(ctx, errF, "open file failed")
        return
    }
    defer f.Close()

    // 文件大小限制100M
    if confFile.Size > 100*1024*1024 {
        c.error(ctx, ecode.FileUploadSizeOver, nil)
        return
    }

    ret, err := bboxSvc.TaskUpload(f)

    e0 := time.Now()
    d0 := e0.Sub(s0)
    fmt.Printf("time=%v\n", d0)
}
```

``` golang
func (c *taskController) upload(ctx *gin.Context) {
    fmt.Println(time.Now().Format("2006-01-02 15:04:05"))
    s0 := time.Now()

    //if ctx.Request.ContentLength > 400*1024*1024 {
    //  c.error(ctx, ecode.FileUploadSizeOver, nil)
    //  return
    //}
    //
    //uid := c.mustGetLoginUser(ctx).ID
    //
    //mr, err := ctx.Request.MultipartReader()
    //if err != nil {
    //  c.error(ctx, err, "upload file to s3 failed")
    //  return
    //}
    //
    //ret, err := bboxSvc.TaskUpload(uid, mr)

    e0 := time.Now()
    d0 := e0.Sub(s0)
    fmt.Printf("time=%v\n", d0)
}
```



