# p5.js | grid tabanlı bölümleme optimizasyonu

Bu projeyi Bilgisayar Mühendisliğinde Özel Konular dersi için geliştirdim.

optimize edilmemiş                                            /                              optimize edilmiş 

![555 particles without optimization](img/555nonOptim-particle.gif) / ![555 particles optimization](img/555-particle-optimz.gif)

(mavi kutunun içindeki yazı fps :))

Projemde çok sayıda particle ve her particle diğer tüm particle'lar arasında mesafe kontrolü yapıp çizgi çizip çizmeyeceğine karar veriyordu. Bu da oldukça maliyetli bir işlem olup fps'de ciddi düşüşler yaşatıyordu.

Bu problemi çözebilmek için canvas'ı grid'e bölüp bu griddeki her hücre içinde bulunan particle'lar arasında mesafe kontrolü yapmak performansı artırdığını gözlemledim. Aşağıda grid'i nasıl parçaladığıma dair bir çıktı bulunuyor.

<img src="img/gridpartition.png" alt="grid-partition" width="250"/>

Sınıf tanıtımlarında sözde kod yazılmıştır.
## sketch.js


```javascript
function setup() {
  createCanvas(canvas.w, canvas.h);
  colorMode(HSL)
  fpsDisplayer = new FPS(50,50)
  grid = new Grid(canvas.w / cellCount , canvas.w , canvas.h)
  
  for (let i = 0; i < particleCount; i++) {
    particles.push(
      new Particle(grid)
    )
  }
}
function draw() {
    background(0);
    grid.draw();
    particles.forEach(particle => {
      particle.update();
  });
    fpsDisplayer.show();
}
```
## particle.js

bu sınıf ekrandaki her particle'ının özelliklerini, çizim ve hangi grid cell'ine üye olacağını belirlediği kodları içeriyor.

buradaki bazı değişkenler private tanımlanarak performans açısından iyileştirme yapılmak istendi.

veloctiy - position gibi değişkenler canvas genişliğine uygun şekilde random belirleniyor.

particle kendi pozisyonuna göre griddeki bulunan uygun cell'e subscribe oluyor.


```javascript
class Particle {
    constructor(grid) {
        this.#diameter
        this.#radius = this.#diameter / 2;
        this.grid
        this.pos 
        this.#vel
        this.#color
        this.#init();
    }

    #init() {
        this.#canMove = true;
        this.#subscribeToCell();
    }

    #draw() {
        //drawing shape and calculating color
    }

    #subscribeToCell() {
        //grid cell subscribe
    }

    update() {
        this.#draw();
        this.#subscribeToCell();
        if (!this.#canMove) return;
        //some movement code...
        //some collision code..
    }
}
```

  
## cell.js

cell sınıfı gridde bulunacak matris objeleri olarak düşünülebilir.

her cell'in particle kümeleri var ve bu kümelerdeki particlelar gerekli koşulları sağlıyorsa particlelar arası çizgi çizilme işlemi yapılıyor

```javascript
class Cell {
    #maxParticleDistance;
    constructor(i, j, w, h) {
        this.#maxParticleDistance;
        this.particles = new Set();
    }

    draw() {
        //drawing cell box
        //drawing connected Particles
    }

    subscribe(particle) {
        this.particles.add(particle);
    }

    unsubscribe(particle) {
        this.particles.delete(particle);
    }

    #connectParticles() {
        //calculate distance between particleA and particleB
        //if distance less then our value then draw line
    }
}
```
## grid.js

```javascript
class Grid {
    constructor(cellSize, width, height) {
        this.cellSize = cellSize;
        this.cols = Math.ceil(width / cellSize);
        this.rows = Math.ceil(height / cellSize);
        this.cells //matris tanımlamızı burada yapıyoruz

        
    }

    getCell(x, y) {
        //particle tanımlanırken tekil grid nesnesini paremetre olarak alır ve getCell fonksiyonuyla dünyadaki yerini belirler
    }


    draw(){
        //cell'lerin draw fonksiyonu çağrılır
    }
}
```

