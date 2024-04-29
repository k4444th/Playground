<script>
	import { onMount, tick } from 'svelte';

    let parent;
    let canvas;

    let darkGrey = "#0a0a0a"
    let appleGreen = "#0add0a"
    let appleBrown = "#dd5555"
    let primaryColor = "#ff3e00";
    let secondaryColor = "#676778";

    function Block(x, y) {
        this.x = x;
        this.y = y;
    }

    let fillEllipse = (ctx, x, y, w, h) => {
        var kappa = .5522848,
            ox = (w / 2) * kappa, // control point offset horizontal
            oy = (h / 2) * kappa, // control point offset vertical
            xe = x + w,           // x-end
            ye = y + h,           // y-end
            xm = x + w / 2,       // x-middle
            ym = y + h / 2;       // y-middle

        ctx.beginPath();
        ctx.moveTo(x, ym);
        ctx.bezierCurveTo(x, ym - oy, xm - ox, y, xm, y);
        ctx.bezierCurveTo(xm + ox, y, xe, ym - oy, xe, ym);
        ctx.bezierCurveTo(xe, ym + oy, xm + ox, ye, xm, ye);
        ctx.bezierCurveTo(xm - ox, ye, x, ym + oy, x, ym);
        //ctx.closePath(); // not used correctly, see comments (use to close off open path)
        ctx.fill();
    }

    let drawApple = (ctx, blockX, blockY, blockSize) => {
        ctx.fillStyle = appleBrown;
        ctx.fillRect(blockX * blockSize + blockSize * 0.45, blockY * blockSize, blockSize * 0.1, blockSize / 2);
        ctx.fillStyle = appleGreen;
        fillEllipse(ctx, blockX * blockSize, blockY * blockSize + blockSize * 0.15, blockSize * 0.65, blockSize * 0.85);
        fillEllipse(ctx, blockX * blockSize + blockSize * 0.35, blockY * blockSize + blockSize * 0.15, blockSize * 0.65, blockSize * 0.85   );
    }

    let drawSnakeHead = (ctx, head, next, blockSize, snakeWidth) => {
        ctx.fillStyle = primaryColor;
        let startX, startY;
        if (head.x == next.x && head.y > next.y) {
            startX = head.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = head.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (head.x == next.x && head.y < next.y) {
            startX = head.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (head.x > next.x && head.y == next.y) {
            startX = head.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = head.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (head.x < next.x && head.y == next.y) {
            startX = head.x * blockSize + + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
            startY = head.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }

        let height = head.y === next.y ? blockSize / 2 : (blockSize * snakeWidth);
        let width = head.x === next.x ? blockSize / 2 : (blockSize * snakeWidth);

        fillEllipse(ctx, head.x * blockSize + blockSize * (1 - (snakeWidth)) / 2, head.y * blockSize  + blockSize * (1 - (snakeWidth)) / 2, blockSize * snakeWidth, blockSize * snakeWidth);
        ctx.fillRect(startX, startY, height, width)
    }

    let drawSnakeElement = (ctx, before, element, after, blockSize, snakeWidth) => {
        ctx.fillStyle = secondaryColor;
        fillEllipse(ctx, element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2, element.y * blockSize  + blockSize * (1 - (snakeWidth)) / 2, blockSize * snakeWidth, blockSize * snakeWidth);
        
        let startX, startY;
        if (element.x == before.x && element.y > before.y) {
            startX = element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (element.x == before.x && element.y < before.y) {
            startX = element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (element.x > before.x && element.y == before.y) {
            startX = element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (element.x < before.x && element.y == before.y) {
            startX = element.x * blockSize + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }

        let height = element.y === before.y ? blockSize / 2 : (blockSize * snakeWidth);
        let width = element.x === before.x ? blockSize / 2 : (blockSize * snakeWidth);

        ctx.fillRect(startX, startY, height, width);

        if (element.x == after.x && element.y > after.y) {
            startX = element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (element.x == after.x && element.y < after.y) {
            startX = element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (element.x > after.x && element.y == after.y) {
            startX = element.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (element.x < after.x && element.y == after.y) {
            startX = element.x * blockSize + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
            startY = element.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }

        height = element.y === after.y ? blockSize / 2 : (blockSize * snakeWidth);
        width = element.x === after.x ? blockSize / 2 : (blockSize * snakeWidth);

        ctx.fillRect(startX, startY, height, width);
    }

    let drawSnakeTail = (ctx, tail, previous, blockSize, snakeWidth) => {
        ctx.fillStyle = secondaryColor;
        let startX, startY;
        if (tail.x == previous.x && tail.y > previous.y) {
            startX = tail.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = tail.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (tail.x == previous.x && tail.y < previous.y) {
            startX = tail.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = tail.y * blockSize + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (tail.x > previous.x && tail.y == previous.y) {
            startX = tail.x * blockSize + blockSize * (1 - (snakeWidth)) / 2;
            startY = tail.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }
        else if (tail.x < previous.x && tail.y == previous.y) {
            startX = tail.x * blockSize + + blockSize / 2 + blockSize * (1 - (snakeWidth)) / 2;
            startY = tail.y * blockSize + blockSize * (1 - (snakeWidth)) / 2;
        }

        let height = tail.y === previous.y ? blockSize / 2 : (blockSize * snakeWidth);
        let width = tail.x === previous.x ? blockSize / 2 : (blockSize * snakeWidth);

        fillEllipse(ctx, tail.x * blockSize + blockSize * (1 - (snakeWidth)) / 2, tail.y * blockSize  + blockSize * (1 - (snakeWidth)) / 2, blockSize * snakeWidth, blockSize * snakeWidth);
        ctx.fillRect(startX, startY, height, width)
    }

    let drawSnake = (ctx, snakeElements, blockSize, snakeWidth) => {
        drawSnakeHead(ctx, snakeElements[0], snakeElements[1], blockSize, snakeWidth);
        for (let i = 1; i < snakeElements.length - 1; i++) {
            drawSnakeElement(ctx, snakeElements[i - 1], snakeElements[i], snakeElements[i + 1], blockSize, snakeWidth);
        }
        drawSnakeTail(ctx, snakeElements[snakeElements.length - 1], snakeElements[snakeElements.length - 2], blockSize, snakeWidth);
    }

    let generateApplePos = (pos, snakeElements, blocks) => {
        let overlap = true;
        while (overlap) {
            pos.x = Math.floor(Math.random() * blocks);
            pos.y = Math.floor(Math.random() * blocks);
            snakeElements.forEach(element => {
                overlap = false;
                if (element.x == pos.x && element.y == pos.y) overlap = true;
            });
        };
    }

    let printInstructions = (ctx, cHeight, cWidth) => {
        ctx.font = "32px arial";
        ctx.textAlign = "center";
        ctx.fillStyle = "white";
        ctx.fillText("Use the arrow keys to move the snake.", cHeight / 2, cWidth / 3);
    }

    let greyOutGame = (ctx, cHeight, cWidth) => {
        ctx.fillStyle = darkGrey;
        ctx.globalAlpha = 0.6;
        ctx.fillRect(0, 0, cHeight, cWidth);
        ctx.globalAlpha = 1;
    }

    onMount(async () => {
        let ctx = canvas.getContext("2d");
        let cWidth = parent.offsetWidth > 600 ? 600 : parent.offsetWidth;
        canvas.width = cWidth;
        let cHeight = cWidth;
        canvas.height = cHeight;
        
        await tick();

        let blocks = 20;
        let blockSize = cWidth / blocks;

        let snakeElements = [
            new Block(11, 10),
            new Block(10, 10),
            new Block(9, 10)


        ];

        let snakeWidth = 0.8;

        let applePos = new Block(0, 0);
        
        generateApplePos(applePos, snakeElements, blocks);
        drawApple(ctx, applePos.x, applePos.y, blockSize);
        drawSnake(ctx, snakeElements, blockSize, snakeWidth);
        greyOutGame(ctx, cHeight, cWidth);
        printInstructions(ctx, cHeight, cWidth);
    });
</script>

<div class="centered" bind:this={parent}>
    <canvas bind:this={canvas} class="canvas"></canvas>
</div>

<style>
    .centered {
        text-align: center;
    }

    .canvas {
        border-style: solid;
        border-color: rgb(25, 25, 25);
        border-width: 2px;
        /* width: 100%;
        max-width: 600px; */
    }
</style>