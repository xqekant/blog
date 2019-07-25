以下是APICloud上传图片的两种方式:拍照,从图片库选择.

html部分:
<!--上传执照  -->
<div class="address-name">
    <div class="">
        <span style="font-size:14px;color:#333333">上传营业执照</span>
    </div>
</div>
<div class="licence-all" tapmode onclick="fnOpenMedia();">
    <img id="licencePic" src="../image/m-camera.png" alt="" style="width:60px;height:60px;border-radius: 30px;" class="la-img">
</div>

js部分:
//1.1 打开拍照功能或者媒体库
function fnOpenMedia() {
    api.actionSheet({
        // title: '',
        cancelTitle: '取消',
        buttons: ['拍照', '从手机相册选择']
    }, function(ret, err) {
        getPicture(ret.buttonIndex);
    });
}

// 1.2 两种方式上传图片
function getPicture(index_) {
    // 拍照
    if (index_ == 1) {
        api.getPicture({
            sourceType: 'camera', // 拍照
            encodingType: 'jpg',
            mediaValue: 'pic',
            destinationType: 'url',
            allowEdit: false,
            quality: 90,
            saveToPhotoAlbum: false
        }, function(ret, err) {
            if (ret.data == '' || ret.data == null) {
                return;
            } else {
                // alert(JSON.stringify(ret.data));
                // 获取到图片将图片上传到服务器
                api.ajax({
                    url: util.url.uploadFile,
                    method: 'post',
                    data: {
                        files: {
                            file: ret.data
                        }
                    }
                }, function(ret, err) {
                    if (ret) {
                        // 给vue中的
                        app.businessLicense = util.ctxt + ret.fileName;
                        //  alert(JSON.stringify(app.imageNameV ));
                    } else {
                        alert(JSON.stringify(err));
                    }
                });
                $api.attr($api.byId('licencePic'), 'src', ret.data);
                $api.attr($api.byId('licencePic'), 'style', "width:100%;height:100%;");
            }
        });

        // 从手机相册选取
    } else if (index_ == 2) {
        api.getPicture({
            sourceType: 'album',
            encodingType: 'jpg',
            mediaValue: 'pic',
            destinationType: 'url',
            // destinationType: 'base64',
            allowEdit: false,
            quality: 90,
            saveToPhotoAlbum: true
        }, function(ret, err) {
            if (ret.data == '' || ret.data == null) {
                return;
            } else {
                // alert(JSON.stringify(ret.data));
                api.ajax({
                    url: util.url.uploadFile,
                    method: 'post',
                    data: {
                        files: {
                            file: ret.data
                        }
                    }
                }, function(ret, err) {
                    if (ret) {
                        // 给vue的
                        app.businessLicense = util.ctxt + ret.fileName;
                        //  alert(JSON.stringify(app.imageNameV ));
                    } else {
                        alert(JSON.stringify(err));
                    }
                });
                $api.attr($api.byId('licencePic'), 'src', ret.data);
                $api.attr($api.byId('licencePic'), 'style', "width:100%;height:100%;");
            }
        });
    }
}

其中,     
$api.attr($api.byId('licencePic'), 'src', ret.data);
就是将本地图片显示到页面上;