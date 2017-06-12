---
layout:     post
title:      "Three.js实现三阶魔方模型 每层可旋转"
subtitle:   ""
date:       2017-06-06 18:42:00
author:     "Wyq"
tags:
   学习 
   three.js
---


<a href="/html/threeJS.html">示例效果</a>

非原创。（左键点击背景拖动可整体旋转魔方。拖动魔方每层可单层旋转。）
[ThreeJS 四步制作一个简易魔方](https://juejin.im/entry/58c0cb452f301e006ba6d7ae)。

研究了下三阶魔方单层旋转的做法。觉得还是three.js实现比较方便。
原文从搭建threeJS通用架子，到画出三阶魔方模型，最后实现单层旋转的监听事件，每一步逻辑都很清晰。推荐初学three.js的新手学习参阅。

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>三阶魔方 three.js</title>
        <script type="text/javascript" src="https://threejs.org/build/three.js"></script>
        <!--视角控制类库  轨道控件-->
        <script type="text/javascript" src="https://threejs.org/examples/js/controls/OrbitControls.js"></script>
        <style type="text/css">
            div#canvas-frame {
                cursor: move;
                width:100%;
                height:100%;
                background-color: black;
            }
            body{
                background: rgb(0,0,0);
                overflow: hidden;
            }
            /*星星效果start*/
            .stars {
                position: absolute;
                top: 50%;
                left: 50%;
                width: 2px;
                height: 2px;
                box-shadow: -447px 387px red, -401px 118px #fafafa, -109px 217px #d9d9d9, -680px -436px #e3e3e3, 514px 360px #cccccc, -708px 298px #e8e8e8, -696px -270px #ededed, 116px -128px #f7f7f7, 179px 35px white, -404px -90px whitesmoke, -331px -309px #c4c4c4, -363px -24px #d1d1d1, 277px 416px #fafafa, -145px -244px whitesmoke, 123px 62px #d4d4d4, -407px 418px #d9d9d9, 535px 237px #d9d9d9, -466px -78px #f7f7f7, 257px 287px #dedede, 327px -398px #e0e0e0, -602px -38px #c2c2c2, 128px 398px #e6e6e6, 274px -446px #d1d1d1, -602px -298px #c7c7c7, 526px -5px #c4c4c4, -90px -158px #fcfcfc, 5px 294px whitesmoke, -633px 229px #c4c4c4, -475px 427px #dedede, 586px -453px #f2f2f2, 180px -432px #c7c7c7, -637px -88px #cfcfcf, -453px 308px #d6d6d6, -111px 1px #d9d9d9, 573px -450px #ededed, 198px 300px #d6d6d6, -355px 166px #dedede, -715px 13px #e3e3e3, 262px -104px #d1d1d1, 147px 325px #dbdbdb, 1px 399px #dbdbdb, 286px -100px white, 43px -329px #e8e8e8, 617px 55px #d9d9d9, -168px -392px #cccccc, 84px 219px #c9c9c9, 507px -226px #d9d9d9, -327px -70px #e6e6e6, 386px -212px #c4c4c4, -717px 4px #cfcfcf, 502px -231px #e3e3e3, 302px 56px #ededed, 649px 341px #c7c7c7, 569px 350px #c9c9c9, 516px -31px #e6e6e6, 689px 447px #c2c2c2, 591px -206px #fafafa, 422px -137px #e6e6e6, -510px -324px #cccccc, -649px 287px #c2c2c2, -194px -48px #f7f7f7, -279px -329px #d1d1d1, -406px 478px #dbdbdb, -735px -87px #c9c9c9, 30px -197px #dedede, -564px 233px #e6e6e6, -486px -324px #ededed, -54px -7px #ededed, -441px -194px #e3e3e3, -133px -95px #e0e0e0, -722px -73px #d6d6d6, 595px 423px #ededed, 568px -39px #ededed, 370px 377px #d1d1d1, -419px -102px #fcfcfc, -450px 109px #c4c4c4, -57px -119px #d1d1d1, -582px 150px #e6e6e6, 206px -263px #cfcfcf, 582px -461px #c9c9c9, -268px -141px #d9d9d9, -148px 291px #c7c7c7, 254px -179px #c9c9c9, 725px 424px #f0f0f0, 391px -150px #ebebeb, 89px -299px #d4d4d4, 170px 1px #c9c9c9, 243px 209px #c7c7c7, 27px 460px #c9c9c9, -465px -380px #d4d4d4, 530px -360px whitesmoke, -626px 53px #e0e0e0, 706px 218px #d9d9d9, 40px -82px #cccccc, -5px -212px #e6e6e6, -742px 33px #ebebeb, -714px 478px #e0e0e0, -585px -125px #cccccc, -216px 348px #cfcfcf, 601px 332px #ededed, 344px -88px #c4c4c4, 659px -22px #d1d1d1, -411px 188px #d6d6d6, -423px -206px #fcfcfc, -359px -136px #cfcfcf, 612px 406px whitesmoke, 725px 96px whitesmoke, 363px -446px white, -204px 325px #c9c9c9, 740px 176px #fafafa, -489px -352px white, -638px 64px #dbdbdb, 537px -65px #dbdbdb, 151px -32px #ebebeb, 681px 212px #fcfcfc, 604px -149px #e6e6e6, -542px -398px #c4c4c4, -707px 66px whitesmoke, -381px 258px #cfcfcf, -30px 332px #d6d6d6, 512px -381px #c9c9c9, 195px 288px #cccccc, -278px 479px #c7c7c7, 27px -208px #d6d6d6, -288px 15px white, -680px 248px #dedede, 433px 31px #c9c9c9, 150px -206px #d4d4d4, -79px 247px white, -594px 115px #e0e0e0, 99px 292px #e0e0e0, 673px -269px #dedede, -257px -64px #d1d1d1, 449px 81px #f2f2f2, 18px -99px #d1d1d1, -694px 415px #f7f7f7, 240px 264px #e0e0e0, 450px -172px white, 383px 7px #e8e8e8, 338px -73px #c9c9c9, 291px -19px #ebebeb, 659px 137px #d1d1d1, 602px -6px #fcfcfc, 554px 249px #ebebeb, 625px 356px #d9d9d9, 579px -183px #d6d6d6, -20px 250px white, -401px 431px #c4c4c4, -645px -232px #cccccc, -265px -148px white, 553px 258px #d1d1d1, 166px -360px #ebebeb, 719px 51px #ededed, 612px -129px #ebebeb, -465px -104px #f2f2f2, -154px -121px #d9d9d9, -1px 330px #f2f2f2, -666px 248px #f7f7f7, -720px 264px #ededed, 148px -365px #e6e6e6, -388px -349px #c4c4c4, 128px -88px #e3e3e3, -683px -274px #fafafa, -341px 41px #c9c9c9, -59px -471px #f0f0f0, -3px -427px #c2c2c2, 418px 167px #d6d6d6, 343px 247px #c7c7c7, 623px -347px #d1d1d1, 716px -217px white, 243px -409px whitesmoke, -75px -126px #d6d6d6, -730px -91px #c9c9c9, -210px -397px #cfcfcf, -349px 180px #c9c9c9, -567px -281px #e0e0e0, -460px 381px #fcfcfc, -310px -22px #ededed, 450px -1px #dbdbdb, -405px -328px #e3e3e3, 5px 332px #d6d6d6, -294px 302px #fcfcfc, -398px 97px whitesmoke, -696px 325px #cfcfcf, -589px 110px #d6d6d6, 353px -411px #dbdbdb, -697px -318px #ebebeb, -114px -72px #f0f0f0, 259px -193px #fcfcfc, 60px 26px #e6e6e6, -63px -232px white, 205px -372px #f7f7f7, -464px -333px #f2f2f2, -374px 123px white, -377px -386px #c7c7c7, -80px 337px #cccccc, 478px -178px #dbdbdb, 222px 420px #ebebeb, -707px 99px #c4c4c4, 716px -132px #fafafa, -253px -286px #e3e3e3, 646px 178px #f0f0f0, 201px 24px #d1d1d1, 178px -58px #c7c7c7, -557px 368px #ededed, 0px 219px #d9d9d9, -266px -269px #cccccc, 242px -197px #c9c9c9, -419px 193px #c2c2c2, -47px 91px #c7c7c7, -109px 75px #c2c2c2, -146px -453px #d6d6d6, 671px -350px #f2f2f2, 421px -91px #d9d9d9, 738px 19px #ededed, -316px -155px #dedede, 419px 244px #fcfcfc, -278px -418px #d6d6d6, -581px -181px #fcfcfc, 139px 264px #d9d9d9, 691px -11px #ebebeb, -622px 402px #c2c2c2, 219px 396px #f0f0f0, -149px -423px white, -716px -78px #d9d9d9, -590px 341px #e6e6e6, -208px 79px #d6d6d6, -227px -24px #f7f7f7, 239px 262px #d1d1d1, 740px 443px #f7f7f7, 509px 134px #d6d6d6, -555px 232px #e8e8e8, -67px -427px #cfcfcf, -368px 250px #f7f7f7, 715px -415px #fafafa, 411px -301px #f0f0f0, -322px 287px #d9d9d9, -429px -90px #f2f2f2, -327px -387px #f0f0f0, -491px 183px #c2c2c2, -133px 250px #d4d4d4, 538px 139px #e3e3e3, -417px -125px #f0f0f0, 653px -351px #e6e6e6, -549px 38px #d4d4d4, 602px 110px whitesmoke, 415px 105px #e0e0e0, -733px -371px #cfcfcf, 286px 403px #d4d4d4, 11px 320px #c4c4c4, -597px 158px whitesmoke, 716px -350px whitesmoke, 321px 67px #fafafa, -237px -300px #cfcfcf, 74px 152px #c9c9c9, 587px -123px #fcfcfc, 699px -332px whitesmoke, 399px 355px #f7f7f7, -323px 314px #dbdbdb, 89px 416px #c7c7c7, 445px 38px #e3e3e3, 572px 122px #c4c4c4, -258px 372px white, 49px 306px #d9d9d9, 437px -35px #dedede, 566px 174px #f2f2f2, 732px -299px whitesmoke, -410px 394px #ededed, 131px -415px white, 19px -326px #e8e8e8, -700px -188px #d1d1d1, 96px -1px #e0e0e0, -328px -396px #f0f0f0, -117px -214px #fcfcfc, -53px 261px #ebebeb, 80px 134px #d6d6d6, -364px -216px white, -636px -125px #dbdbdb, -639px -265px #e3e3e3, 208px 98px #c7c7c7, 172px 467px #e0e0e0, 435px 309px #e3e3e3, 194px -259px #f0f0f0, 209px -186px #c9c9c9, -312px 418px #fafafa, 229px 407px #c9c9c9, -449px -357px #fafafa, 674px 121px #e8e8e8, 608px -429px #ebebeb, -431px -428px #cfcfcf, 105px 462px #e3e3e3, -179px -372px #e3e3e3, 143px -317px #d6d6d6, -449px -149px #fafafa, -544px 250px #dedede, -220px -323px whitesmoke, 658px 8px whitesmoke, -656px -244px #e8e8e8, 347px 11px whitesmoke, 694px -230px #f7f7f7, -317px 1px #c4c4c4, 28px 23px #fcfcfc, -382px 321px #dbdbdb, 632px -74px #c4c4c4, 154px -245px #c2c2c2, -553px 337px #d6d6d6, -48px -243px #d1d1d1, 92px -391px #cccccc, -71px -256px #cfcfcf, -372px 57px #d9d9d9, 369px -140px #fcfcfc, 675px 81px #c2c2c2, -663px 254px #cccccc, 703px -203px #ededed, 74px -363px #c2c2c2, 643px -458px #d1d1d1, 198px 359px #cccccc, 265px 309px #d4d4d4, -353px -368px #e8e8e8, -465px 439px whitesmoke, 693px 360px #c9c9c9, 634px -397px #d1d1d1, 467px 25px whitesmoke, -558px -272px #e6e6e6, 671px 69px #dbdbdb, 407px 357px #cfcfcf, 379px 80px white, 10px -203px #c9c9c9, 104px -292px #f0f0f0, -667px -29px #d1d1d1, 557px -155px #e6e6e6, -505px 115px #cfcfcf, -605px 164px #f2f2f2, -108px -223px #e0e0e0, 523px -156px #ebebeb, 691px 230px white, -507px -13px #d1d1d1, -349px 332px #dedede, 520px 266px whitesmoke, -66px -250px #e6e6e6, -496px -449px #ebebeb, 414px -170px #dedede, -649px 230px #ebebeb, 598px -92px #c7c7c7, -638px 113px #c2c2c2, 151px 363px #f7f7f7, -445px -241px #f0f0f0, 527px -14px #dedede, 203px -61px #cfcfcf, -716px -284px #ebebeb, -525px 134px #c2c2c2;
                animation: fly 60s linear infinite;
                transform-style: preserve-3d;
            }

            .stars:before,
            .stars:after {
                content: "";
                position: absolute;
                width: inherit;
                height: inherit;
                box-shadow: inherit;
            }

            .stars:before {
                transform: translateZ(-300px);
                opacity: .6;
            }

            .stars:after {
                transform: translateZ(-600px);
                opacity: .4;
            }

            @keyframes fly {
                from {
                    transform: rotateZ(360deg);
                    opacity: .6;
                }
                to {
                    transform: rotateZ(0deg);
                    opacity: 1;
                }
            }
            /*星星效果end*/
        </style>
    </head>
    <script>
        var renderer;//渲染器
        var width;//宽度
        var height;//高度
        var raycaster = new THREE.Raycaster();//光线碰撞检测器
        var mouse = new THREE.Vector2();//存储鼠标坐标或者触摸坐标
        var isRotating = false;//魔方是否正在转动
        var intersect;//碰撞光线穿过的元素
        var normalize;//触发平面法向量
        var startPoint;//触发点
        var movePoint;
        var initStatus = [];//魔方初始状态
        //魔方转动的六个方向
        var xLine = new THREE.Vector3( 1, 0, 0 );//X轴正方向
        var xLineAd = new THREE.Vector3( -1, 0, 0 );//X轴负方向
        var yLine = new THREE.Vector3( 0, 1, 0 );//Y轴正方向
        var yLineAd = new THREE.Vector3( 0, -1, 0 );//Y轴负方向
        var zLine = new THREE.Vector3( 0, 0, 1 );//Z轴正方向
        var zLineAd = new THREE.Vector3( 0, 0, -1 );//Z轴负方向

        //兼容requestAnimationFrame
        window.requestAnimFrame = (function(){
            return window.requestAnimationFrame ||
                    window.mozRequestAnimationFrame ||
                    window.webkitRequestAnimationFrame ||
                    window.msRequestAnimationFrame ||
                    window.webkitRequestAnimationFrame;
        })();

        //根据页面宽度和高度创建渲染器，并添加容器中
        function initThree() {
            width = window.innerWidth;
            height = window.innerHeight;
            renderer = new THREE.WebGLRenderer({antialias : true});//生成渲染器对象
            renderer.setSize(width, height);//指定渲染器的高宽
            renderer.setClearColor(0x000000, 1.0);//设置canvas背景色
            document.getElementById('canvas-frame').appendChild(renderer.domElement);//追加 【canvas】 元素到 【canvas3d】 元素中
        }

        //创建相机，并设置正方向和中心点
        var camera;
        function initCamera() {
            camera = new THREE.PerspectiveCamera(45, width / height, 1, 1000);
            camera.position.x = 300;
            camera.position.y = 300;
            camera.position.z = 300;
            camera.up.x = 0;//正方向
            camera.up.y = 1;
            camera.up.z = 0;
            camera.lookAt({
                x : 0,
                y : 0,
                z : 0
            });
            //视角控制
            controller = new THREE.OrbitControls(camera, renderer.domElement);
            controller.target = new THREE.Vector3(0, 0, -75);//设置控制点
        }

        //创建场景，后续元素需要加入到场景中才会显示出来
        var scene;
        function initScene() {
            scene = new THREE.Scene();
        }

        //创建光线
        var light;
        function initLight() {
            light = new THREE.AmbientLight(0xfefefe);
            scene.add(light);
        }

        var cubeParams = {//魔方参数
            x:-75,
            y:75,
            z:75,
            num:3,
            len:50,
            colors:['green','blue',
                    'orange','red',
            'yellow','white']
        };

        /**
         * 魔方
         * x、y、z 魔方正面左上角坐标
         * num 魔方单位方向上数量
         * len 魔方单位正方体宽高
         * colors 魔方六面体颜色
         */
        function SimpleCube(x,y,z,num,len,colors){
            var cubes = [];
            for(var i=0;i<num;i++){
                for(var j=0;j<num*num;j++){
                    var cubegeo = new THREE.BoxGeometry(len,len,len);
                    var materials = [];
                    var myFaces = [];
                    //一个小正方体有六个面，每个面使用相同材质的纹理，但是颜色不一样
                    myFaces.push(faces(colors[0]));
                    myFaces.push(faces(colors[1]));
                    myFaces.push(faces(colors[2]));
                    myFaces.push(faces(colors[3]));
                    myFaces.push(faces(colors[4]));
                    myFaces.push(faces(colors[5]));
                    for (var k = 0; k < 6; k++) {
                        var texture = new THREE.Texture(myFaces[k]);
                        texture.needsUpdate = true;
                        materials.push(new THREE.MeshLambertMaterial({
                            map: texture
                        }));
                    }
                    var cubemat = new THREE.MeshFaceMaterial(materials);
                    var cube = new THREE.Mesh( cubegeo, cubemat );
                    //假设整个魔方的中心在坐标系原点，推出每个小正方体的中心
                    cube.position.x = (x+len/2)+(j%3)*len;
                    cube.position.y = (y-len/2)-parseInt(j/3)*len;
                    cube.position.z = (z-len/2)-i*len;
                    cubes.push(cube)
                }
            }
            return cubes;
        }

        //生成canvas素材
        function faces(rgbaColor) {
            var canvas = document.createElement('canvas');
            canvas.width = 256;
            canvas.height = 256;
            var context = canvas.getContext('2d');
            if (context) {
                //画一个宽高都是256的黑色正方形
                context.fillStyle = 'rgba(0,0,0,1)';
                context.fillRect(0, 0, 256, 256);
                //在内部用某颜色的16px宽的线再画一个宽高为224的圆角正方形并用改颜色填充
                context.rect(16, 16, 224, 224);
                context.lineJoin = 'round';
                context.lineWidth = 16;
                context.fillStyle = rgbaColor;
                context.strokeStyle = rgbaColor;
                context.stroke();
                context.fill();
            } else {
                alert('您的浏览器不支持Canvas无法预览.\n');
            }
            return canvas;
        }

        //创建展示场景所需的各种元素
        var cubes
        function initObject() {
            //生成魔方小正方体
            cubes = SimpleCube(cubeParams.x,cubeParams.y,cubeParams.z,cubeParams.num,cubeParams.len,cubeParams.colors);
            for(var i=0;i<cubes.length;i++){
                var item = cubes[i];
                /**
                 * 由于筛选运动元素时是根据物体的id规律来的，但是滚动之后位置发生了变化；
                 * 再根据初始规律筛选会出问题，而且id是只读变量；
                 * 所以这里给每个物体设置一个额外变量cubeIndex，每次滚动之后更新根据初始状态更新该cubeIndex；
                 * 让该变量一直保持初始规律即可。
                 */
                initStatus.push({
                    x:item.position.x,
                    y:item.position.y,
                    z:item.position.z,
                    cubeIndex:item.id
                });
                item.cubeIndex = item.id;
                scene.add(cubes[i]);//并依次加入到场景中
            }

            //透明正方体
            var cubegeo = new THREE.BoxGeometry(150,150,150);
            var hex = 0x000000;
            for ( var i = 0; i < cubegeo.faces.length; i += 2 ) {
                cubegeo.faces[ i ].color.setHex( hex );
                cubegeo.faces[ i + 1 ].color.setHex( hex );
            }
            var cubemat = new THREE.MeshBasicMaterial({vertexColors: THREE.FaceColors,opacity: 0, transparent: true});
            var cube = new THREE.Mesh( cubegeo, cubemat );
            cube.cubeType = 'coverCube';
            scene.add( cube );
        }

        //渲染
        function render(){
            renderer.clear();
            renderer.render(scene, camera);
            window.requestAnimFrame(render);
        }

        //开始  入口
        function threeStart(){
            initThree();
            initCamera();
            initScene();
            initLight();
            initObject();
            render();
            //监听鼠标事件
            window.addEventListener('mousedown', startCube, false);
            window.addEventListener('mousemove', moveCube, false );
            window.addEventListener('mouseup', stopCube,false);
            //监听触摸事件
            window.addEventListener('touchstart', startCube, false);
            window.addEventListener('touchmove', moveCube, false);
            window.addEventListener('touchmove', moveCube, false);
        }

        //魔方操作结束
        function stopCube(){
            intersect = null;
            startPoint = null
        }

        //绕着世界坐标系的某个轴旋转
        function rotateAroundWorldY(obj,rad){
            var x0 = obj.position.x;
            var z0 = obj.position.z;
            /**
             * 因为物体本身的坐标系是随着物体的变化而变化的，
             * 所以如果使用rotateZ、rotateY、rotateX等方法，
             * 多次调用后就会出问题，先改为Quaternion实现。
             */
            var q = new THREE.Quaternion();
            q.setFromAxisAngle( new THREE.Vector3( 0, 1, 0 ), rad );
            obj.quaternion.premultiply( q );
            //obj.rotateY(rad);
            obj.position.x = Math.cos(rad)*x0+Math.sin(rad)*z0;
            obj.position.z = Math.cos(rad)*z0-Math.sin(rad)*x0;
        }
        function rotateAroundWorldZ(obj,rad){
            var x0 = obj.position.x;
            var y0 = obj.position.y;
            var q = new THREE.Quaternion();
            q.setFromAxisAngle( new THREE.Vector3( 0, 0, 1 ), rad );
            obj.quaternion.premultiply( q );
            //obj.rotateZ(rad);
            obj.position.x = Math.cos(rad)*x0-Math.sin(rad)*y0;
            obj.position.y = Math.cos(rad)*y0+Math.sin(rad)*x0;
        }
        function rotateAroundWorldX(obj,rad){
            var y0 = obj.position.y;
            var z0 = obj.position.z;
            var q = new THREE.Quaternion();
            q.setFromAxisAngle( new THREE.Vector3( 1, 0, 0 ), rad );
            obj.quaternion.premultiply( q );
            //obj.rotateX(rad);
            obj.position.y = Math.cos(rad)*y0-Math.sin(rad)*z0;
            obj.position.z = Math.cos(rad)*z0+Math.sin(rad)*y0;
        }

        //滑动操作魔方
        function moveCube(event){
            getIntersects(event);
            if(intersect){
                if(!isRotating&&startPoint){//魔方没有进行转动且满足进行转动的条件
                    movePoint = intersect.point;
                    if(!movePoint.equals(startPoint)){//和起始点不一样则意味着可以得到转动向量了
                        isRotating = true;//转动标识置为true
                        var sub = movePoint.sub(startPoint);//计算转动向量
                        var direction = getDirection(sub);//获得方向
                        var elements = getBoxs(intersect,direction);
                        var startTime = new Date().getTime();
                        window.requestAnimFrame(function(timestamp){
                            rotateAnimation(elements,direction,timestamp,0);
                        });
                    }
                }
            }
            event.preventDefault();
        }

        /**
         * 旋转动画
         */
        function rotateAnimation(elements,direction,currentstamp,startstamp,laststamp){
            var totalTime = 500;//转动的总运动时间
            if(startstamp===0){
                startstamp = currentstamp;
                laststamp = currentstamp;
            }
            if(currentstamp-startstamp>=totalTime){
                currentstamp = startstamp+totalTime;
                isRotating = false;
                startPoint = null;
                updateCubeIndex(elements);
                //转动之后需要更新魔方此时所处的状态
                console.log(updateCubeStatus());
            }
            switch(direction){
                    //绕z轴顺时针
                case 0.1:
                case 1.2:
                case 2.4:
                case 3.3:
                    for(var i=0;i<elements.length;i++){
                        rotateAroundWorldZ(elements[i],-90*Math.PI/180*(currentstamp-laststamp)/totalTime);
                    }
                    break;
                    //绕z轴逆时针
                case 0.2:
                case 1.1:
                case 2.3:
                case 3.4:
                    for(var i=0;i<elements.length;i++){
                        rotateAroundWorldZ(elements[i],90*Math.PI/180*(currentstamp-laststamp)/totalTime);
                    }
                    break;
                    //绕y轴顺时针
                case 0.4:
                case 1.3:
                case 4.3:
                case 5.4:
                    for(var i=0;i<elements.length;i++){
                        rotateAroundWorldY(elements[i],-90*Math.PI/180*(currentstamp-laststamp)/totalTime);
                    }
                    break;
                    //绕y轴逆时针
                case 1.4:
                case 0.3:
                case 4.4:
                case 5.3:
                    for(var i=0;i<elements.length;i++){
                        rotateAroundWorldY(elements[i],90*Math.PI/180*(currentstamp-laststamp)/totalTime);
                    }
                    break;
                    //绕x轴顺时针
                case 2.2:
                case 3.1:
                case 4.1:
                case 5.2:
                    for(var i=0;i<elements.length;i++){
                        rotateAroundWorldX(elements[i],90*Math.PI/180*(currentstamp-laststamp)/totalTime);
                    }
                    break;
                    //绕x轴逆时针
                case 2.1:
                case 3.2:
                case 4.2:
                case 5.1:
                    for(var i=0;i<elements.length;i++){
                        rotateAroundWorldX(elements[i],-90*Math.PI/180*(currentstamp-laststamp)/totalTime);
                    }
                    break;
                default:
                    break;
            }
            if(currentstamp-startstamp<totalTime){
                window.requestAnimFrame(function(timestamp){
                    rotateAnimation(elements,direction,timestamp,startstamp,currentstamp);
                });
            }
        }

        //更新位置索引
        function updateCubeIndex(elements){
            for(var i=0;i<elements.length;i++){
                var temp1 = elements[i];
                for(var j=0;j<initStatus.length;j++){
                    var temp2 = initStatus[j];
                    if( Math.abs(temp1.position.x - temp2.x)<=cubeParams.len/2 &&
                            Math.abs(temp1.position.y - temp2.y)<=cubeParams.len/2 &&
                            Math.abs(temp1.position.z - temp2.z)<=cubeParams.len/2 ){
                        temp1.cubeIndex = temp2.cubeIndex;
                        break;
                    }
                }
            }
        }

        /**
         * 更新魔方状态
         * 假设初始化时按固定位置给魔方编号，还原之后位置和编号不变
         */
        function updateCubeStatus(){
            for(var i=0;i<cubes.length;i++){
                var item = cubes[i];
                if(item.id!==item.cubeIndex){
                    return false;
                }
            }
            return true;
        }

        //根据方向获得运动元素
        function getBoxs(target,direction){
            var targetId = target.object.cubeIndex;
            var ids = [];
            for(var i=0;i<cubes.length;i++){
                ids.push(cubes[i].cubeIndex);
            }
            var minId = min(ids);
            targetId = targetId-minId;
            var numI = parseInt(targetId/9);
            var numJ = targetId%9;
            var boxs = [];
            //根据绘制时的规律判断 no = i*9+j
            switch(direction){
                    //绕z轴
                case 0.1:
                case 0.2:
                case 1.1:
                case 1.2:
                case 2.3:
                case 2.4:
                case 3.3:
                case 3.4:
                    for(var i=0;i<cubes.length;i++){
                        var tempId = cubes[i].cubeIndex-minId;
                        if(numI===parseInt(tempId/9)){
                            boxs.push(cubes[i]);
                        }
                    }
                    break;
                    //绕y轴
                case 0.3:
                case 0.4:
                case 1.3:
                case 1.4:
                case 4.3:
                case 4.4:
                case 5.3:
                case 5.4:
                    for(var i=0;i<cubes.length;i++){
                        var tempId = cubes[i].cubeIndex-minId;
                        if(parseInt(numJ/3)===parseInt(tempId%9/3)){
                            boxs.push(cubes[i]);
                        }
                    }
                    break;
                    //绕x轴
                case 2.1:
                case 2.2:
                case 3.1:
                case 3.2:
                case 4.1:
                case 4.2:
                case 5.1:
                case 5.2:
                    for(var i=0;i<cubes.length;i++){
                        var tempId = cubes[i].cubeIndex-minId;
                        if(tempId%9%3===numJ%3){
                            boxs.push(cubes[i]);
                        }
                    }
                    break;
                default:
                    break;
            }
            return boxs;
        }

        //获得旋转方向
        function getDirection(vector3){
            var direction;
            //判断差向量和x、y、z轴的夹角
            var xAngle = vector3.angleTo(xLine);
            var xAngleAd = vector3.angleTo(xLineAd);
            var yAngle = vector3.angleTo(yLine);
            var yAngleAd = vector3.angleTo(yLineAd);
            var zAngle = vector3.angleTo(zLine);
            var zAngleAd = vector3.angleTo(zLineAd);
            var minAngle = min([xAngle,xAngleAd,yAngle,yAngleAd,zAngle,zAngleAd]);//最小夹角
            switch(minAngle){
                case xAngle:
                    direction = 0;//向x轴正方向旋转90度（还要区分是绕z轴还是绕y轴）
                    if(normalize.equals(yLine)){
                        direction = direction+0.1;//绕z轴顺时针
                    }else if(normalize.equals(yLineAd)){
                        direction = direction+0.2;//绕z轴逆时针
                    }else if(normalize.equals(zLine)){
                        direction = direction+0.3;//绕y轴逆时针
                    }else{
                        direction = direction+0.4;//绕y轴顺时针
                    }
                    break;
                case xAngleAd:
                    direction = 1;//向x轴反方向旋转90度
                    if(normalize.equals(yLine)){
                        direction = direction+0.1;//绕z轴逆时针
                    }else if(normalize.equals(yLineAd)){
                        direction = direction+0.2;//绕z轴顺时针
                    }else if(normalize.equals(zLine)){
                        direction = direction+0.3;//绕y轴顺时针
                    }else{
                        direction = direction+0.4;//绕y轴逆时针
                    }
                    break;
                case yAngle:
                    direction = 2;//向y轴正方向旋转90度
                    if(normalize.equals(zLine)){
                        direction = direction+0.1;//绕x轴逆时针
                    }else if(normalize.equals(zLineAd)){
                        direction = direction+0.2;//绕x轴顺时针
                    }else if(normalize.equals(xLine)){
                        direction = direction+0.3;//绕z轴逆时针
                    }else{
                        direction = direction+0.4;//绕z轴顺时针
                    }
                    break;
                case yAngleAd:
                    direction = 3;//向y轴反方向旋转90度
                    if(normalize.equals(zLine)){
                        direction = direction+0.1;//绕x轴顺时针
                    }else if(normalize.equals(zLineAd)){
                        direction = direction+0.2;//绕x轴逆时针
                    }else if(normalize.equals(xLine)){
                        direction = direction+0.3;//绕z轴顺时针
                    }else{
                        direction = direction+0.4;//绕z轴逆时针
                    }
                    break;
                case zAngle:
                    direction = 4;//向z轴正方向旋转90度
                    if(normalize.equals(yLine)){
                        direction = direction+0.1;//绕x轴顺时针
                    }else if(normalize.equals(yLineAd)){
                        direction = direction+0.2;//绕x轴逆时针
                    }else if(normalize.equals(xLine)){
                        direction = direction+0.3;//绕y轴顺时针
                    }else{
                        direction = direction+0.4;//绕y轴逆时针
                    }
                    break;
                case zAngleAd:
                    direction = 5;//向z轴反方向旋转90度
                    if(normalize.equals(yLine)){
                        direction = direction+0.1;//绕x轴逆时针
                    }else if(normalize.equals(yLineAd)){
                        direction = direction+0.2;//绕x轴顺时针
                    }else if(normalize.equals(xLine)){
                        direction = direction+0.3;//绕y轴逆时针
                    }else{
                        direction = direction+0.4;//绕y轴顺时针
                    }
                    break;
                default:
                    break;
            }
            return direction;
        }

        //获取数组中的最小值
        function min(arr){
            var min = arr[0];
            for(var i=1;i<arr.length;i++){
                if(arr[i]<min){
                    min = arr[i];
                }
            }
            return min;
        }

        //开始操作魔方
        function startCube(event){
            getIntersects(event);
            //魔方没有处于转动过程中且存在碰撞物体
            if(!isRotating&&intersect){
                startPoint = intersect.point;//开始转动，设置起始点
            }
        }

        //获取操作焦点以及该焦点所在平面的法向量
        function getIntersects(event){
            //触摸事件和鼠标事件获得坐标的方式有点区别
            if(event.touches){
                var touch = event.touches[0];
                mouse.x = (touch.clientX / width)*2 - 1;
                mouse.y = -(touch.clientY / height)*2 + 1;
            }else{
                mouse.x = (event.clientX / width)*2 - 1;
                mouse.y = -(event.clientY / height)*2 + 1;
            }
            raycaster.setFromCamera(mouse, camera);
            //Raycaster方式定位选取元素，可能会选取多个，以第一个为准
            var intersects = raycaster.intersectObjects(scene.children);
            //如果操作焦点在魔方上则取消移动，反之恢复移动
            if(intersects.length){
                try{
                    if(intersects[0].object.cubeType==='coverCube'){
                        intersect = intersects[1];
                        normalize = intersects[0].face.normal;
                    }else{
                        intersect = intersects[0];
                        normalize = intersects[1].face.normal;
                    }
                }catch(err){
                    //nothing
                }
                controller.enabled = false;//焦点在魔方上时禁止视角变换
            }else{
                controller.enabled = true;
            }
        }
    </script>
    <body onload="threeStart();">
    <div class="stars"></div>
    <div id="canvas-frame"></div>
    </body>
    </html>

        
源码放到[前端网](http://www.qdfuns.com/notes/39886/587f58b428949862ee9e63a2691a5f31.html)上了,欢迎收藏。