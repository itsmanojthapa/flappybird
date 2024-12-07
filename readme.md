### **1. Game Setup**

The game starts by setting up the canvas, loading images, and initializing objects like the bird and pipes.

#### **Canvas Setup**

```javascript
const canvas = document.getElementById("app");
canvas.width = 400;
canvas.height = 600;
const ctx = canvas.getContext("2d");
```

- **`canvas`** : The HTML `<canvas>` element where the game is rendered.
- **`ctx`** : The **2D rendering context** , which provides methods to draw shapes, images, and text.

---

### **2. Images**

Images are preloaded to render the background, bird, pipes, and game-over message:

```javascript
const bgImg = new Image();
bgImg.src = "./images/background.png";
```

---

### **3. Bird Object**

The bird's properties are stored in an object:

```javascript
const bird = {
  x: 50, //x & y: Position of the bird.
  y: 300,
  velocity: 0, // The bird's speed along the Y-axis.
  gravity: 0.5, //A constant force pulling the bird downward.
  jumpForce: -8, //The upward force applied when the bird "jumps."
  size: 50, // The bird's size (width and height).
};
```

---

### **4. Pipes (Using Arrays)**

Pipes are stored in an array, and each pipe has:

```javascript
const pipes = [];
const pipeWidth = 60;
const pipeGap = 200;
```

- **`pipes`** : An array holding all active pipes.
- **`pipeWidth`** : Fixed width of pipes.
- **`pipeGap`** : The vertical gap between the upper and lower pipes.

Each pipe is represented as an object:

```javascript
pipes.push({
  x: canvas.width, // Starts at the far right
  y: Math.random() * (canvas.height - pipeGap - 100) + 50, // Random height
  passed: false, // Tracks if the pipe has been passed by the bird
});
```

```javascript
// Draw pipes
for (let pipe of pipes) {
  // Draw the upper pipe
  ctx.drawImage(pillerUpImg, pipe.x, 0, pipeWidth, pipe.y);

  // Draw the lower pipe
  ctx.drawImage(
    pillerDownImg,
    pipe.x,
    pipe.y + pipeGap,
    pipeWidth,
    canvas.height - (pipe.y + pipeGap)
  );
}
```

---

### **5. Game Loop**

The **game loop** is the heart of the game:

```javascript
function gameLoop() {
  update();
  draw();
  requestAnimationFrame(gameLoop);
}
```

- **`update()`** : Handles game logic (bird movement, pipe generation, collision checks, etc.).
- **`draw()`** : Renders the updated game state on the canvas.
- **`requestAnimationFrame()`** : Ensures smooth animations by repeatedly calling `gameLoop`.

---

### **6. Update Logic**

The `update()` function modifies the game state:

```javascript
bird.velocity += bird.gravity; // Simulate gravity
bird.y += bird.velocity; // Move bird
```

- The bird's `velocity` increases due to gravity, pulling it downward.

#### **Pipe Movement**

```javascript
pipe.x -= 2; // Move pipes to the left
```

- Pipes move leftward, creating the illusion of forward movement.

#### **Pipe Generation**

```javascript
if (pipes.length === 0 || pipes[pipes.length - 1].x < canvas.width - 200) {
  const pipeY = Math.random() * (canvas.height - pipeGap - 100) + 50;
  pipes.push({ x: canvas.width, y: pipeY, passed: false });
}
```

- New pipes are generated when:

  - The array is empty.
  - The last pipe has moved sufficiently left.

- (canvas.height - pipeGap - 100):

  - This calculates the maximum vertical range where the top pipe's position can be placed.
  - Subtracting pipeGap ensures there’s enough room below the top pipe for the gap.
  - Subtracting 100 further restricts the range to avoid placing pipes too close to the bottom of the canvas, ensuring gameplay fairness and aesthetic consistency.

- +50
  - Without this, the top pipe could be at y = 0, which might visually clip or overlap with other elements.

---

### **7. Collision Detection**

This checks if the bird collides with a pipe or goes out of bounds:

```javascript
if (
  bird.x + bird.size > pipe.x && // Right side of bird > Left side of pipe
  bird.x < pipe.x + pipeWidth && // Left side of bird < Right side of pipe
  (bird.y < pipe.y || bird.y + bird.size > pipe.y + pipeGap) // Y-axis check
) {
  gameOver = true; // Collision occurred
}
```

#### **Boundary Check**

```javascript
if (bird.y < 0 || bird.y + bird.size > canvas.height) {
  gameOver = true; // Bird hits the top or bottom boundary
}
```

---

### **8. Scoring**

Points are awarded when the bird successfully passes a pipe:

```javascript
if (!pipe.passed && pipe.x + pipeWidth < bird.x) {
  score++;
  pipe.passed = true; // Mark pipe as passed
}
```

---

### 9. Event Handling

The `keydown` event listens for the spacebar to make the bird jump or restart the game.

```javascript
function handleEvent(event) {
  if (event.code === "Space" || "click") {
    if (!start) {
      start = true;
    }
    if (gameOver) {
      // Reset game
      bird.y = 300;
      bird.velocity = 0;
      pipes.length = 0;
      score = 0;
      gameOver = false;
    } else {
      bird.velocity = bird.jumpForce;
    }
  }
}
```

---
