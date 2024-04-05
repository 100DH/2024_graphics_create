# 2024_graphics_create
지형 만들기 과제
### 20210815 백대현 지형만들기 및 작품 과제
---------------------------
<img src=https://github.com/100DH/2024_graphics_create/assets/93199016/96c155ab-3c27-4e2a-94fd-d257e1d4bbe2>

---------------------------

### 작성 소감

여태까지는 단순한 도형을 이용해서 만들어내다가 vertex를 이용해서 3D지형을 만드는 것을 하니 생각보다 매우 어려웠지만,
이것을 이용해서 앞으로 또 어떤것을 만들 수 있을지 기대가 되었고, 여태까지 배운 내용들을 이용해 모양을 만들고,
하나의 작품을 만들어내는 과정에서 배운 내용들을 다시 복습할 수 있어서 좋았습니다. 또 한편으로는 지금껏 배운것은
시작에 불과하다는 생각이 들었고, 앞으로 배우는 내용들도 잘 학습하여서 모든 코드들을 완전히 내 것으로 만들고 좋은 작품을
만들 수 있도록 노력해야겠다고 생각하게 되었습니다.

---------------------------

### 자가 채점

1. 배경과 지형의 색상과 투명도를 변경시키시오.
   
이 부분에서는 배를 이동시키며 배의 이동위치에 따라서 색을 변경하기위해 배가 앞으로 가면 해가 내려가면서 일몰의 느낌을 주기위해
배경의 색을 주황색 계열로 변경하였고, 3D 지형으로 바다를 표현하여 바다의 색깔 또한 푸른색에서 태양빛을 받아 색이 어두워지도록 하였고,
뒤로가면 다시 푸른색으로 돌아오게 만들었습니다.
투명도는 배가 앞으로 가면 점점 멀어져 안보이는 것을 표현하기위해 배의 투명도를 높여서 흐려지도록 만들었습니다.

2.도형 만들기

vertex와 box, cylinder,를 이용하여 배의 모양을 만들어주었습니다.

3.재질을 추가하시오

specularMaterial을 이용하여 빛에 따라서 배의 색이 변할 수 있도록 하였습니다.

4.조명을 추가하시오

directionalLight를 이용하여 배에 빛을 비춰주도록 만들었고, 구체 하나를 만들어 해를 표현해 빛을 내뿜는것처럼 보여주기 위해서
구체에 빛을 비춰주었습니다.

--------------------------

### 코드
        //20210815 백대현 
        let cols, rows;
        let scl = 20;
        let w = 1500;
        let h = 1500;
        
        let flying = 0;
        
        let terrain = [];
        let x = 0;
        let y = 0;
        let xoff = 0;
        let yoff = 0;
        
        let shipx = 0;
        let shipz = 0;
        
        let colorR = 0;
        let colorG = 0;
        let colorB = 0;
        
        function setup() {
          createCanvas(800, 700, WEBGL);
          cols = w / scl;
          rows = h / scl;
        
          for (let x = 0; x < cols; x++) {
            terrain[x] = [];
            for (let y = 0; y < rows; y++) {
              terrain[x][y] = 0; //specify a default value for now
            }
          }
        }
        
        function draw() {
          
          orbitControl();
          push();
            noStroke();
            flying -= 0.05;
            let yoff = flying;
            for (let y = 0; y < rows; y++) {
              let xoff = 0;
              for (let x = 0; x < cols; x++) {
                terrain[x][y] = map(noise(xoff, yoff), 0, 2, -100, 100);
                xoff += 0.2;
              }
              yoff += 0.2;
            }
        
        
            background(0-shipz/2,155,255+shipz/2);
            translate(0, 50);
            rotateX(PI / 2);
            translate(-w / 2, -h / 2);
            for (let y = 0; y < rows - 1; y++) {
              beginShape(TRIANGLE_STRIP);
              for (let x = 0; x < cols; x++) {
                let v = terrain[x][y];
                fill(v-shipz/5,v+70-shipz/10,v+120+shipz/10);
                vertex(x * scl, y * scl, terrain[x][y]);
                vertex(x * scl, (y + 1) * scl, terrain[x][y + 1]);
                
              }
              endShape();
           }
          pop();  
          
          push();
          
          translate(shipx,0,shipz);
          push();
          
            translate(0,terrain[x][y]/4+55,0); //지형에 따른 배의 각도 변화
            rotateY(PI);
            if(terrain[x][y] < terrain[x][y+1]){
              rotateX(PI/35);
            }
            else if (terrain[x][y] == terrain[x][y+1]){
              rotateX(0);
            }
            else if (terrain[x][y] > terrain[x][y+1]){
              rotateX(-PI/35);
            }
          
          if(keyIsDown(UP_ARROW)){ //배 위치 이동
            shipz -= 10
          }
          if(keyIsDown(DOWN_ARROW)){ 
            shipz += 10;
          }
          if(keyIsDown(RIGHT_ARROW)){ 
            shipx += 7;
          }
          if(keyIsDown(LEFT_ARROW)){ 
            shipx -= 7;
          }
            
            directionalLight(100,100,100,200+shipx/7,-200,200+shipz/10); ///배에 비춰지는 빛
            directionalLight(100,100,100,0+shipx/7,-200,0+shipz/10);
            directionalLight(100,100,100,-200+shipx/7,-200,-200+shipz/10);
            directionalLight(100,100,100,200+shipx/7,0,-200+shipz/10);
            
            fill(0,0,0,shipz+255); //배의 투명도 결정
            scale(0.3);  
            ship();
          pop();
          pop();
          
          
          push();
          translate(0,-shipz,-200);
          sun();
          pop();
          
          if(shipz >= 600){ //최대 위치 제한
            shipz = 600;
          }
          if(shipz <= -600){
            shipz = -600;
          }
          if(shipx >= 600){
            shipx = 600;
          }
          if(shipx <= -600){
            shipx = -600;
          }
          
        }
        
        function ship() { //
          let x = 50; // 배 X축 길이
          let y = 50; // 배 Y축 길이
          let z = 120; //배 Z축 길이
          
          push();
            push(); //배 몸통
              
              
              specularMaterial(200,50,50);
              beginShape();//배 앞면
              vertex(-100-x,0,200+z);
              vertex(-100-x,80+y,150+z);
              vertex(0,80+y,200+z);
              vertex(0,0,300+z);
              endShape(CLOSE);
              beginShape();
              vertex(0,80+y,200+z);
              vertex(100+x,80+y,150+z);
              vertex(100+x,0,200+z);
              vertex(0,0,300+z);
              endShape(CLOSE);
        
              beginShape();//배 뒷면
              vertex(-100-x,0,-200-z);
              vertex(-100-x,80+y,-150-z);
              vertex(100+x,80+y,-150-z);
              vertex(100+x,0,-200-z);
              endShape(CLOSE);
        
              beginShape();//배 오른쪽면
              vertex(100+x,0,200+z);
              vertex(100+x,0,-200-z);
              vertex(100+x,80+y,-150-z);
              vertex(100+x,80+y,150+z);
              endShape(CLOSE);
        
              beginShape();//배 왼쪽면
              vertex(-100-x,0,200+z);
              vertex(-100-x,0,-200-z);
              vertex(-100-x,80+y,-150-z);
              vertex(-100-x,80+y,150+z);
              endShape(CLOSE);
        
              beginShape();//배 밑면
              vertex(-100-x,80+y,-150-z);
              vertex(100+x,80+y,-150-z);
              vertex(100+x,80+y,150+z);
              vertex(0,80+y,200+z);
              vertex(-100-x,80+y,150+z);
              endShape(CLOSE);
        
              //밑판
              specularMaterial(153,102,51);
              beginShape();
              vertex(-96-x,63+y,-153-z);
              vertex(96+x,63+y,-153-z);
              vertex(96+x,63+y,154+z);
              vertex(0,63+y,204+z);
              vertex(-96-x,63+y,154+z);
              endShape(CLOSE);
            pop();
        
            push();//선실 창문
              specularMaterial(80,80,80);
              translate(0,-20,77);
              box(60,40,5)
              translate(-70,0,0);
              box(60,40,5)
              translate(140,0,0);
              box(60,40,5)
            pop();
        
            push();//선실 문
              specularMaterial(80,80,80);
              translate(0,40,-175);
              box(80,150,5)
              noStroke();
              specularMaterial(0,0,0);
              rotateX(PI/2);
              translate(-25,0,0);
              cylinder(5,10,10);
            pop();
        
            push();//선실
              specularMaterial(240,240,240);
              translate(0,25,-50);
              box(220,200,250)
            pop();
        
            push();//선실 지붕
              specularMaterial(240,240,240);
              translate(0,-80,-50);
              box(260,30,330);
            pop();
          pop();
        }
        
        function sun(){
          
          noStroke();
          directionalLight(255,153,51,0,100,-100)
          directionalLight(255,153,51,300,0,-300)
          directionalLight(255,153,51,-100,0,-300)
          directionalLight(255,153,51,0,-100,-100)
          directionalLight(255,153,51,0,0,-1000)
          specularMaterial(255,153,51);
          translate(-200,-500,-700);
          ellipsoid(100,100,100);
          
        }
