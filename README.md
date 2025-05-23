const svg = document.querySelector("svg");
const cursor = svg.createSVGPoint();

const arrows = document.querySelector(".arrows");

const target = { x: 900, y: 249.5 }; // Target center
const pivot = { x: 100, y: 250 };    // Bow rotation point

// Initial aim to show bow facing cursor
aim({ clientX: 320, clientY: 300 });

window.addEventListener("mousedown", draw);

function draw(e) {
  // Simulate random pullback angle
  const randomAngle = (Math.random() - 0.5) * Math.PI * 0.03;

  // Show arrow
  TweenMax.to(".arrow-angle use", 0.3, { opacity: 1 });

  // Start aiming with cursor
  window.addEventListener("mousemove", aim);

  // Fire on mouseup
  window.addEventListener("mouseup", loose);

  aim(e);
}

function aim(e) {
  cursor.x = e.clientX;
  cursor.y = e.clientY;

  const point = cursor.matrixTransform(svg.getScreenCTM().inverse());
  const dx = point.x - pivot.x;
  const dy = point.y - pivot.y;

  const angle = Math.atan2(dy, dx);

  // Rotate bow or arrow towards angle
  document.querySelector(".bow").setAttribute("transform", `rotate(${angle * 180 / Math.PI}, ${pivot.x}, ${pivot.y})`);
}

function loose() {
  // Fire the arrow with animation
  TweenMax.to(".arrow-angle use", 0.5, {
    x: target.x,
    y: target.y,
    opacity: 0,
    ease: Power2.easeOut,
    onComplete: resetArrow
  });

  window.removeEventListener("mousemove", aim);
  window.removeEventListener("mouseup", loose);
}

function resetArrow() {
  // Reset arrow position
  TweenMax.set(".arrow-angle use", {
    x: pivot.x,
    y: pivot.y,
    opacity: 1
  });
}
