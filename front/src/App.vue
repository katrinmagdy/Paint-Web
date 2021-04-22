<template>
 <div id="app">
    

   <div id="options">
     <img class="undo" src="./assets/undo.png"  @click="onUndoClick()">
     <label class= "labels">undo</label>
     <img class="redo" src="./assets/redo.png" @click="onRedoClick()">
     <label class= "labels">redo</label>
     <img class="save" src="./assets/save.png" @click="onSaveClick()">
     <label class= "labels">save</label>
     <img class="load" src="./assets/load.png" @click="onLoadClick()">
     <label class= "labels">load</label>
     <input class ="input" type="text" id="lineThickness" value="1" >
     <label class= "labels">line thickness</label>
     <input class ="input" type="color" id="color" >
     <label class= "labels">color</label>
</div>

<canvas ref="canvas" id="drawing-canvas" > </canvas>

  <div id = "tools-container"> 
 <div class="title"><span class="first-letter">T</span>ools</div>
<img id="pencil" title="pencil" :style="isPencilClicked ? { 'background-color': '#ff4d4d' } : null" @click="onPencilClick()" src="./assets/pencil.png">
<img id="eraser" title="eraser" :style="isEraserClicked ? { 'background-color': '#ff4d4d' } : null" @click="onEraserClick()" src="./assets/eraser.png">
<img id="resize" title="resize" :style="isResizeClicked ? { 'background-color': '#ff4d4d' } : null" @click="onResizeClick()" src="./assets/resize.png">
<img id="delete"  title="delete" :style="isDeleteClicked ? { 'background-color': '#ff4d4d' } : null" @click="onDeleteClick()" src="./assets/delete.png">
<img id="line"   title="line" :style="isLineClicked ? { 'background-color': '#ff4d4d' } : null" @click="onLineClick()" src="./assets/line.png">
<img id="triangle"   title="triangle" :style="isTriangleClicked ? { 'background-color': '#ff4d4d' } : null" @click="onTriangleClick()" src="./assets/triangle.png">
<img id="circle"  title="circle" :style="isCircleClicked ? { 'background-color': '#ff4d4d' } : null" @click="onCircleClick()" src="./assets/circle.png">
<img id="ellipse" title="ellipse" :style="isEllipseClicked ? { 'background-color': '#ff4d4d' } : null" @click="onEllipseClick()" src="./assets/ellipse.png">

<img id="rectangle" title="rectangle" :style="isRectangleClicked ? { 'background-color': '#ff4d4d' } : null" @click="onRectangleClick()" src="./assets/rectangle.png">
<img id="square"  title="square"  :style="isSquareClicked ? { 'background-color': '#ff4d4d' } : null" @click="onSquareClick()" src="./assets/square.png">
<img id="color-palette" title="color-palette" :style="isColorPaletteClicked ? { 'background-color': '#ff4d4d' } : null" @click="onColorPaletteClick()" src="./assets/color-palette.png">
<img id="line-thickness"  title="line-thickness" :style="isLineThicknessClicked ? { 'background-color': '#ff4d4d' } : null" @click="onLineThicknessClick()" src="./assets/width.png">
<img id="move"  title="move" :style="isMoveClicked ? { 'background-color': '#ff4d4d' } : null" @click="onMoveClick()" src="./assets/move.png">
<img id="copy"  title="copy"  :style="isCopyClicked ? { 'background-color': '#ff4d4d' } : null" @click="onCopyClick()" src="./assets/copy.png">

  </div>
 
  </div>
</template>

<script>
import  axios  from 'axios'
export default {
  name: 'App',
 data (){
   return {
     lineThickness:"1",
     color: null,
     fill : false,

     isPencilClicked :false,
     isEraserClicked :false,
     isLineClicked :false,
     isCircleClicked :false,
     isEllipseClicked :false,
     isResizeClicked :false,
     isRectangleClicked:false,
     isSquareClicked :false,
     isColorPaletteClicked :false,
     isDeleteClicked :false,
     isMoveClicked :false,
     isCopyClicked :false,
     isTriangleClicked :false,
     isLineThicknessClicked : false ,


      isUndoClicked : false,
     isRedoClicked : false,
     isLoadClicked : false ,
     isSaveClicked : false ,

     canvas:null,
     context: null,
     isDrawing: false,
     startX: 0,
     startY: 0,
      x:0,
      y:0,
      points: [],

     id:0,

     extra : [],

     fx:0,
     fy:0,
     tx:0,
     ty:0,

     fistclick:false,
    
}},

 mounted() {
        var vm = this;
        vm.canvas = vm.$refs.canvas;
        vm.context = vm.canvas.getContext("2d");
        vm.canvas.width=1200;
        vm.canvas.height=670;
        vm.canvas.addEventListener("mousedown", vm.mousedown);
        vm.canvas.addEventListener("mousemove", vm.mousemove);
        document.addEventListener("mouseup", vm.mouseup);

    },
   methods : {

     refreshdata(){
       var pex=[];
       for(var i=0;i<this.extra.length;i++){
         pex.push({ id:this.extra[i][0] , draw:this.extra[i][7], x1:this.extra[i][1], y1:this.extra[i][2], x2:this.extra[i][3], y2:this.extra[i][4] ,color:this.extra[i][5],lineThickness:this.extra[i][6] , fill:this.extra[i][8] })
       }

       this.points=pex;

       this.redrawStoredLines();

     },


     show_selected:function() {
          var selector = document.getElementById("color");
          this.color = selector.value;
          var selector2 = document.getElementById("lineThickness");
          this.lineThickness = selector2.value;
        },

     setUncliked:function(){
     this.isPencilClicked = false;
     this. isEraserClicked = false;
     this. isLineClicked = false;
     this.isCircleClicked = false;
     this.isEllipseClicked = false;
     this.isResizeClicked = false;
     this.isRectangleClicked = false;
     this.isSquareClicked = false;
     this.isColorPaletteClicked = false;
     this.isDeleteClicked = false;
     this.isMoveClicked = false;
     this.isCopyClicked = false;
     this.isTriangleClicked =false;
     this.isLineThicknessClicked = false ;
     },
     onResizeClick:function(){
       this.setUncliked();
       this.isResizeClicked=true;
     },
     onLineThicknessClick:function(){
       this.setUncliked();
       this.isLineThicknessClicked=true;
     },
     onUndoClick :function(){
      this .isUndoClicked = true;
        axios.get("http://localhost:8085/undo",{
         
        }).then(response=>{
          this.extra=response.data;

          this.refreshdata();

        this.redrawStoredLines(); 
         //this.isResizeClicked=false;
        })       
     },

    onRedoClick :function(){
      this.isRedoClicked = true;
       axios.get("http://localhost:8085/redo",{
         
        }).then(response=>{
          this.extra=response.data;

          this.refreshdata();

        this.redrawStoredLines(); 
         //this.isResizeClicked=false;
        })       
     },

   onSaveClick :function(){
      this.isSaveClicked = true ;

      let Name = prompt("enter file name","");
      let filePath = prompt("enter file path","");
      let Type = prompt("enter file type","");

      axios.get("http://localhost:8085/save", {params:{
          fileName:Name,
          path:filePath,
          fileType:Type,
        }
      })
          .then(function (response) {
            console.log(response);
          })
          .catch(function (error) {
            console.log(error);
          });
    },

    onLoadClick :function(){
      this.isLoadClicked = true;
      let Name = prompt("enter file name","");
      let filePath = prompt("enter file path","");
      let Type = prompt("enter file type","");
      axios.get("http://localhost:8085/load", {
          params:{
            path : filePath,
            fileName : Name,
            fileType: Type,
        },
      }).then(response=>{
        this.extra=response.data;

        this.refreshdata();
        this.redrawStoredLines();
      })
    },

     onPencilClick:function(){
       this.setUncliked();
       this.isPencilClicked=true;
     },
     onEraserClick:function(){
       this.setUncliked();
       this.isEraserClicked=true;
     },
     onLineClick:function(){
       this.setUncliked();
       this.isLineClicked=true;
     },
     onCircleClick:function(){
       this.setUncliked();
       this.isCircleClicked=true;
     },
     onEllipseClick:function(){
       this.setUncliked();
       this.isEllipseClicked=true;
     },
     onSelectClick:function(){
       this.setUncliked();
       this.isSelectClicked=true;
     },
     onRectangleClick:function(){
       this.setUncliked();
       this.isRectangleClicked=true;
     },
     onSquareClick:function(){
       this.setUncliked();
       this.isSquareClicked=true;
     
     },
     onColorPaletteClick:function(){
       this.setUncliked();
       this.isColorPaletteClicked=true;
     },
     onDeleteClick:function(){
       this.setUncliked();
       this.isDeleteClicked=true;
     },
     onMoveClick:function(){
       this.setUncliked();
       this.isMoveClicked=true;

     },
     onCopyClick:function(){
       this.setUncliked();
       this.isCopyClicked=true;
     },

     onTriangleClick:function(){
       this.setUncliked();
       this.isTriangleClicked=true;
     },

     mousedown(e){
       this.show_selected();
            var vm = this
            var rect = vm.canvas.getBoundingClientRect();
            var x = e.clientX - rect.left;
            var y = e.clientY - rect.top;

            if(vm.isMoveClicked){
              if(vm.fistclick){
                vm.tx=x;
                vm.ty=y;
                axios.get("http://localhost:8085/move",{
                   params : {
                   ox:vm.fx,
                   oy:vm.fy,
                   nx:vm.tx,
                   ny:vm.ty,
  
                   }
                  }).then(response=>{
                     this.extra=response.data;
                      vm.refreshdata();
                      vm.redrawStoredLines(); 

                     })

                    vm.fistclick=false;

              }
              else{
                vm.fx=x;
                vm.fy=y;
                vm.fistclick=true;
              }
            }
            else if(vm.isCopyClicked){

              if(vm.fistclick){
                vm.tx=x;
                vm.ty=y;
                axios.get("http://localhost:8085/copy",{
                      params : {
                      ox:vm.fx,
                      oy:vm.fy,
                      nx:vm.tx,
                      ny:vm.ty,
                      id:vm.id,

                     }
                }).then(response=>{
                    this.extra=response.data;

                    vm.refreshdata();
                    this.id=this.points.length;
                    vm.redrawStoredLines(); 

                     })

                  vm.fistclick=false;
              }
              else{
                vm.fx=x;
                vm.fy=y;
                vm.fistclick=true;
              }

            }
            else if(vm.isResizeClicked){
                vm.tx=x;
                vm.ty=y;
                let endX = prompt("enter the end point x you need","100");
                let endY = prompt("enter the end point y you need","100");
                axios.get("http://localhost:8085/resize",{
          params : {
            fcx:vm.tx,
            fcy:vm.ty,
            sx:endX,
            sy:endY,

          }
        }).then(response=>{
          this.extra=response.data;

          vm.refreshdata();

        vm.redrawStoredLines(); 
         //this.isResizeClicked=false;
        })       
            }
            else if(vm.isLineThicknessClicked){
                vm.tx=x;
                vm.ty=y;
               this.show_selected();
               
                axios.get("http://localhost:8085/modify",{
          params : {
            fcx:vm.tx,
            fcy:vm.ty,
            modificationType:"line thickness",
             modificationValue : this.lineThickness,

          }
        }).then(response=>{
          this.extra=response.data;

          vm.refreshdata();

        vm.redrawStoredLines(); 
         //this.isResizeClicked=false;
        })       
          } 
           else if(vm.isColorPaletteClicked){
                vm.tx=x;
                vm.ty=y;
               this.show_selected();
               
                axios.get("http://localhost:8085/modify",{
          params : {
            fcx:vm.tx,
            fcy:vm.ty,
            modificationType:"color",
             modificationValue : this.color,

          }
        }).then(response=>{
          this.extra=response.data;

          vm.refreshdata();

        vm.redrawStoredLines(); 
         //this.isResizeClicked=false;
        })       
            }
            else if(vm.isDeleteClicked){
                vm.tx=x;
                vm.ty=y;
                axios.get("http://localhost:8085/delete",{
          params : {
            fcx:vm.tx,
            fcy:vm.ty,

          }
        }).then(response=>{
          this.extra=response.data;

          vm.refreshdata();

        vm.redrawStoredLines(); 
         //this.isResizeClicked=false;
        })       
            }
            else{
              vm.isDrawing = true;
              vm.startX=x;
              vm.startY=y;

            }
        },

        mousemove(e){
            this.show_selected();
            this.redrawStoredLines();
            var vm = this
            var rect = vm.canvas.getBoundingClientRect();
            var x = e.clientX - rect.left;
            var y = e.clientY - rect.top;

            if(vm.isDrawing){
              if(vm.isLineClicked){
                 vm.context.beginPath();
                vm.context.moveTo(vm.startX, vm.startY);
                vm.context.lineTo(x,y);
                vm.context.strokeStyle = this.color;
                vm.context.lineWidth = parseFloat(this.lineThickness);
                vm.context.stroke(); 
                vm.x=x;
                vm.y=y;
              }
              else if(vm.isRectangleClicked){
                 vm.context.beginPath();
                 vm.context.strokeStyle = this.color;
                vm.context.lineWidth = parseFloat(this.lineThickness);
                vm.context.moveTo(vm.startX, vm.startY);
                vm.context.strokeRect(vm.startX, vm.startY, x - vm.startX, y - vm.startY);
                vm.context.stroke(); 
                vm.x=x;
                vm.y=y;

              }
              else if(vm.isSquareClicked){
                 vm.context.beginPath();
                 vm.context.strokeStyle = this.color;
                vm.context.lineWidth = parseFloat(this.lineThickness);
                vm.context.moveTo(vm.startX, vm.startY);
                var t1 = x - vm.startX;
                var t2 = y - vm.startY;
                if((t1*t2)<0){
                  vm.context.strokeRect(vm.startX, vm.startY,  x - vm.startX, -(x - vm.startX));
                }
                else{
                  vm.context.strokeRect(vm.startX, vm.startY,  x - vm.startX, x - vm.startX);
                }
                vm.context.stroke(); 
                vm.x=x;
                vm.y=y;

              }
              else if(vm.isCircleClicked){
                vm.context.beginPath();
                vm.context.strokeStyle = this.color;
                vm.context.lineWidth = parseFloat(this.lineThickness);
                var r =Math.sqrt(Math.pow(x - vm.startX, 2)+Math.pow(y - vm.startY, 2));
                vm.context.arc(vm.startX, vm.startY, r,0,2 * Math.PI);
                vm.context.stroke(); 
                vm.x=x;
                vm.y=y;

              }
              else if(vm.isEllipseClicked){
                vm.context.beginPath();
                vm.context.strokeStyle = this.color;
                vm.context.lineWidth = parseFloat(this.lineThickness);
                vm.context.ellipse(vm.startX,vm.startY,Math.abs(vm.startX-x),Math.abs(vm.startY-y), 0, 0, 2 * Math.PI) ;
                vm.context.stroke(); 
                vm.x=x;
                vm.y=y;
              }
              else if(vm.isTriangleClicked){
                vm.context.beginPath();
                vm.context.moveTo(vm.startX, vm.startY);
                vm.context.lineTo(x,vm.startY);
                vm.context.lineTo(x/2,y);
                vm.context.closePath();
                vm.context.strokeStyle = this.color;
                vm.context.lineWidth = parseFloat(this.lineThickness);
                vm.context.stroke(); 
                vm.x=x;
                vm.y=y;
              }
                   
            }
        },

        senddata(draw){
          var vm = this;

          axios.get("http://localhost:8085/getInstanceOfShape",{
          params : {
            id:vm.id,
            name:draw,
            x1 : vm.startX,
            y1 : vm.startY,
            x2 : vm.x,
            y2 : vm.y,
            color : vm.color,
            thick : vm.lineThickness,
            filled : vm.fill

          }
        })

        },

        
        mouseup(e){
      var vm = this
            var rect = vm.canvas.getBoundingClientRect();
            var x = e.clientX - rect.left;
            var y = e.clientY - rect.top;
            vm.x=x;
            vm.y=y;
      if(!((vm.startX==0 && vm.startY==0 && vm.x==0 && vm.y==0)||(vm.x==vm.startX && vm.y==vm.startY)||(vm.x>1200)||(vm.x<0)||(vm.y<0)||(vm.y>670))){
        if(vm.isLineClicked){
         vm.points.push( { id:vm.id , draw:"line", x1:vm.startX, y1:vm.startY, x2:vm.x, y2:vm.y ,color:vm.color,lineThickness:vm.lineThickness ,fill:vm.fill } );
         this.senddata("line");
         vm.id=vm.id+1;
      }
      else if(vm.isRectangleClicked){
         vm.points.push( { id:vm.id , draw:"rectangle", x1:vm.startX, y1:vm.startY, x2:vm.x, y2:vm.y ,color:vm.color,lineThickness:vm.lineThickness ,fill:vm.fill } );
         this.senddata("rectangle");
         vm.id=vm.id+1;
      }
      else if(vm.isSquareClicked){
         vm.points.push( { id:vm.id , draw:"square", x1:vm.startX, y1:vm.startY, x2:vm.x, y2:vm.y ,color:vm.color,lineThickness:vm.lineThickness ,fill:vm.fill} );
         this.senddata("square");
         vm.id=vm.id+1;
      }
      else if(vm.isCircleClicked){
        vm.points.push( { id:vm.id ,draw:"circle", x1:vm.startX, y1:vm.startY, x2:vm.x, y2:vm.y ,color:vm.color,lineThickness:vm.lineThickness ,fill:vm.fill} );
        this.senddata("circle");
        vm.id=vm.id+1;
      }
      else if(vm.isEllipseClicked){
        vm.points.push( { id:vm.id ,draw:"ellipse", x1:vm.startX, y1:vm.startY, x2:vm.x, y2:vm.y ,color:vm.color,lineThickness:vm.lineThickness ,fill:vm.fill } );
        this.senddata("ellipse");
        vm.id=vm.id+1;
      }
      else if(vm.isTriangleClicked){
        vm.points.push( { id:vm.id ,draw:"triangle", x1:vm.startX, y1:vm.startY, x2:vm.x, y2:vm.y ,color:vm.color,lineThickness:vm.lineThickness ,fill:vm.fill} );
        this.senddata("triangle");
        vm.id=vm.id+1;
      } 

      }
      
      this.redrawStoredLines();
      vm.isDrawing = false;
      vm.startX=0;
      vm.startY=0;
      vm.x=0;
      vm.y=0;
    },

     redrawStoredLines(){
         var vm = this
        vm.context.clearRect(0,0,vm.canvas.width,vm.canvas.height);
        if(vm.points.length==0){ return; }
        // redraw each stored line
        for(var i=0;i<vm.points.length;i++){
          if(vm.points[i].draw=="line"){
            vm.context.beginPath();
            vm.context.moveTo(vm.points[i].x1,vm.points[i].y1);
            vm.context.lineTo(vm.points[i].x2,vm.points[i].y2);
            vm.context.strokeStyle= vm.points[i].color;
            vm.context.lineWidth = parseFloat(vm.points[i].lineThickness);
            if(vm.points[i].fill){
              vm.context.fillStyle = vm.points[i].color;
              vm.context.fill();
            }
              vm.context.stroke();
            
            
          }
          else if(vm.points[i].draw=="rectangle"){
            vm.context.beginPath();
            vm.context.strokeStyle= vm.points[i].color;
            vm.context.lineWidth = parseFloat(vm.points[i].lineThickness);
            vm.context.moveTo(vm.points[i].x1,vm.points[i].y1);
            vm.context.rect(vm.points[i].x1 , vm.points[i].y1, vm.points[i].x2 -vm.points[i].x1 ,vm.points[i].y2-vm.points[i].y1);
            if(vm.points[i].fill){
              vm.context.fillStyle = vm.points[i].color;
              vm.context.fill();
            }
            vm.context.stroke();

          }
          else if(vm.points[i].draw=="square"){
            vm.context.beginPath();
            vm.context.strokeStyle= vm.points[i].color;
            vm.context.lineWidth = parseFloat(vm.points[i].lineThickness);
            vm.context.moveTo(vm.points[i].x1,vm.points[i].y1);
            var t1 = vm.points[i].x2 -vm.points[i].x1;
            var t2 = vm.points[i].y2 -vm.points[i].y1;
            if((t1*t2)<0){
              vm.context.rect(vm.points[i].x1 , vm.points[i].y1,vm.points[i].x2 -vm.points[i].x1 ,-(vm.points[i].x2 -vm.points[i].x1));
            }
            else{
              vm.context.rect(vm.points[i].x1 , vm.points[i].y1,vm.points[i].x2 -vm.points[i].x1 , vm.points[i].x2 -vm.points[i].x1);
            }
            if(vm.points[i].fill){
              vm.context.fillStyle = vm.points[i].color;
              vm.context.fill();
            }
            vm.context.stroke();

          }
          else if(vm.points[i].draw=="circle"){
            vm.context.beginPath();
            vm.context.strokeStyle= vm.points[i].color;
            vm.context.lineWidth = parseFloat(vm.points[i].lineThickness);
            var r =Math.sqrt(Math.pow(vm.points[i].x2 - vm.points[i].x1, 2)+Math.pow(vm.points[i].y2 - vm.points[i].y1, 2));
            vm.context.arc(vm.points[i].x1, vm.points[i].y1, r,0,2 * Math.PI);
            if(vm.points[i].fill){
              vm.context.fillStyle = vm.points[i].color;
              vm.context.fill();
            }
            vm.context.stroke(); 
          }
          else if(vm.points[i].draw=="ellipse"){
            vm.context.beginPath();
            vm.context.strokeStyle= vm.points[i].color;
            vm.context.lineWidth = parseFloat(vm.points[i].lineThickness);
            vm.context.ellipse(vm.points[i].x1, vm.points[i].y1 ,Math.abs(vm.points[i].x1-vm.points[i].x2),Math.abs(vm.points[i].y1-vm.points[i].y2), 0, 0, 2 * Math.PI) ;
            if(vm.points[i].fill){
              vm.context.fillStyle = vm.points[i].color;
              vm.context.fill();
            }
            vm.context.stroke(); 
          }
          else if(vm.points[i].draw=="triangle"){
            vm.context.beginPath();
            vm.context.moveTo(vm.points[i].x1,vm.points[i].y1);
            vm.context.lineTo(vm.points[i].x2,vm.points[i].y1);
            vm.context.lineTo(vm.points[i].x2/2,vm.points[i].y2);
            vm.context.closePath();
            vm.context.strokeStyle = vm.points[i].color;
            vm.context.lineWidth = parseFloat(vm.points[i].lineThickness);
            if(vm.points[i].fill){
              vm.context.fillStyle = vm.points[i].color;
              vm.context.fill();
            }
            vm.context.stroke(); 

          } 
        }
    },

}
}
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #0c0c0c;
  margin-top: 10px;
}
.first-letter{
  font-size: 100px;
  line-height: 70px;
  font-family:  serif;
  font-style : bold ;
  color : #111111;
}
#tools-container{
padding-top:10px;
padding-left:20px;
width: 200px ;
height: 660px;
background-color: #565f5f;
border:2px solid #000000;
display :grid;
grid-template-columns: auto auto ;
grid-template-rows:  auto auto auto auto auto auto auto auto auto;
float:left;
}
.title{
   grid-column: 1/3;
   font-size: 40px;
   color : #b91a1a;
}
#options{
  display:flex;
  flex-direction: row;
  background-color: #553e3e;
}
.labels{
  margin-top :10px;
  font-size :30px;
  font-family:Courier;
  color:#f3e7e7;
  margin-right: 20px;
 
}
.undo{
   margin-right: 20px;
}
.redo{
   margin-right: 20px;
}
.save{
   margin-right: 20px;
}
.load{
  margin-right: 20px;
}
#drawing-canvas{
  cursor:move;
 /* width :300px ;
  height:670px ;*/
 border:3px solid #000000;
 
}

.input{
  margin-left: 5px;
  margin-top: 15px;
  margin-right: 10px;
  height: 20px;
  width: 30px;
  background-color: #565f5f;
  font-size:20px;
}
</style>