<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
    <style type="text/css">
        body, html,#allmap {width: 100%;height: 100%;overflow: hidden;margin:0;font-family:"微软雅黑";}
    </style>
    <script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&ak=填上你自己的百度应用AK"></script>
    <title>GPS转百度</title>
</head>
<body>
<div id="allmap"></div>
</body>
</html>
<script type="text/javascript">
    // 百度地图API功能
    //地图初始化
    var bm = new BMap.Map("allmap");
    bm.centerAndZoom("郑州", 12);
    bm.addControl(new BMap.NavigationControl()); // 添加平移缩放控件
    //bm.addControl(new BMap.ScaleControl());                    // 添加比例尺控件
    bm.enableScrollWheelZoom();   //启用滚轮放大缩小，默认禁用
    bm.enableContinuousZoom();    //启用地图惯性拖拽，默认禁用

    var sy = new BMap.Symbol(BMap_Symbol_SHAPE_BACKWARD_OPEN_ARROW, {
        scale: 0.6,//图标缩放大小
        strokeColor:'#fff',//设置矢量图标的线填充颜色
        strokeWeight: '2',//设置线宽
    });
    var icons = new BMap.IconSequence(sy, '10', '30');


    var polyline;
    var flag = 0;
    function  drowLine(point)
    {
        if (flag)
        {
            //alert(flag);
            polyline.setPath(point);
        }
        else
        {
            flag = 1;
            polyline =new BMap.Polyline(point, {
                //enableEditing: false,//是否启用线编辑，默认为false
                //enableClicking: true,//是否响应点击事件，默认为true
                //icons:[icons],
                strokeWeight:'3',//折线的宽度，以像素为单位
                strokeOpacity: 0.8,//折线的透明度，取值范围0 - 1
                strokeColor:"#18a45b" //折线颜色
            });
            bm.addOverlay(polyline);
        }

    }

    var marker;
    //坐标转换完之后的回调函数
    translateCallback = function (data){
        if(data.status === 0) {

            if (marker)
            {
                marker.setPosition(data.points[0]);
                poisArray.push(data.points[0]);
                drowLine(poisArray);
                if ( poisArray.length >= 10000)	 //防止内存一直增加，会卡，大于1w条时，清空一下
                {
                    poisArray=[];
                    poisArray.push(data.points[0]);
                }

    }
            else
            {
                poisArray.push(data.points[0]);
                marker = new BMap.Marker(data.points[0]);
                bm.addOverlay(marker);
            }

            bm.setCenter(data.points[0]);
        }
    }

    var polylineAttr = [];
    var poisArray = [];
    function doLocal(longitude, latitude)  //给qt调用的接口
    {
        var OPoint = new BMap.Point(longitude,latitude);

        var convertor = new BMap.Convertor();
        var pointArr = [];
        pointArr.push(OPoint);
        convertor.translate(pointArr, 1, 5, translateCallback);
    }
</script>
