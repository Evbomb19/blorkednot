import javax.swing.*;
import java.awt.*;
import java.awt.image.*;
import java.awt.event.*;
import java.net.*;
import java.util.*;

public class Main{
    public static int[][] board = new int[8][8]; //int is converted into one of 8 colors
    public static Block[] heldBlocks = new Block[3];
    
    public static Block grabbedBlock = null;
    public static boolean validPlacement = false;
    public static int placeGrabbed = 0;
    
    public static int movesMade = 0;
    public static boolean gameOver = false; //use r to restart
    public static int gameOverSpeed = 1; //used to decide what to skip during the game over screen
    
    //0 for home screen, 1 for normal, 2 for chaos, 3 for survival
    public static int gamemode = 0;
    public static boolean updateMode = false;
    
    
    //survival mode stuff
    public static int nextDrop = 3;
    public static Block[] dropBlocks = new Block[5000];
    public static int[] dropBlocksX = new int[5000];
    public static int[] dropBlocksY = new int[5000];
    public static int playerX = 3;
    public static int playerY = 3;
    public static int playerMoves = 3;
    public static boolean playerGrabbed = false;
    public static boolean validPlayerPlacement = false;
    
    
    public static boolean clearLines = false;
    public static boolean[] clearRows = new boolean[8];
    public static boolean[] clearCols = new boolean[8];
    
    public static int frameWidth = 272;
    public static int frameHeight = 420;
    
    public static int mouseX = 0;
    public static int mouseY = 0;
    
    public static JPanel panel;
    public static JButton b1;
    public static JButton b2;
    public static JButton b3;
    public static JButton b4;
    public static JButton home;
    
    public static Random rand = new Random();
    
    public static void main(String[] args){
        
        JFrame frame = new JFrame("Block Blast - v1.6");
        Container pane = frame.getContentPane();
        pane.setLayout(null);
        
        for(int i = 0; i < 3; i++){
            heldBlocks[i] = Constants.mainBlocks[rand.nextInt(Constants.mainBlocks.length)];
            heldBlocks[i].changeToColor(rand.nextInt(8)+1);
        }
        
        panel = new JPanel(){
            public void paintComponent(Graphics graphics){
                Graphics2D g = (Graphics2D)graphics;
                
                g.setColor(Color.BLUE);
                g.fillRect(0, 0, frameWidth, frameHeight);
                
                
                if(gamemode == 0){
                    
                    g.setColor(Color.WHITE);
                    g.setFont(new Font("Comic sans", Font.BOLD, 24));
                    g.drawString("Block Blast", 30, 50);
                    
                }else{
                    
                    g.setColor(Color.BLACK);
                    g.fillRect(4, 50, 265, 265);
                    
                    g.setColor(Color.WHITE);
                    if(gamemode == 3){
                        g.setFont(new Font("Comic sans", Font.BOLD, 14));
                        g.drawString("Rounds survived: "+movesMade/3, 4, 13);
                        g.drawString("Player moves left: "+playerMoves, 4, 30);
                        g.drawString("Moves until next drop: "+nextDrop, 4, 47);
                    }else{
                        g.setFont(new Font("Comic sans", Font.BOLD, 20));
                        g.drawString("Moves made: "+movesMade, 4, 34);
                    }
                    
                    for(int i = 0; i < 8; i++){
                        for(int j = 0; j < 8; j++){
                            dispTile(board[i][j], j, i, false, true, g);
                        }
                    }
                    
                    if(!gameOver && gamemode == 3){
                        for(int i = 0; i < 5; i++){
                            if(dropBlocks[i] != null){
                                dispBlock(dropBlocks[i], "BlockDrop", dropBlocksX[i], dropBlocksY[i], false, false, g);
                            }
                        }
                        if(playerGrabbed){
                            if(playerMoves > 0){
                                for(int i = -1; i <= 1; i++){
                                    for(int j = -1; j <= 1; j++){
                                        int x = playerX+i;
                                        int y = playerY+j;
                                        if(x >= 0 && x < 8 && y >= 0 && y < 8 && board[y][x] == 0){
                                            dispTile("BlockSmiley", x, y, false, false, g);
                                        }
                                    }
                                }
                            }
                            dispTile("BlockSmiley", mouseX, mouseY, true, true, g);
                        }else{
                            dispTile("BlockSmiley", playerX, playerY, false, true, g);
                        }
                    }
                    
                    for(int i = 0; i < 3; i++){
                        Block block = heldBlocks[i];
                        if(block != null){
                            URL url;
                            for(int row = 0; row < 5; row++){
                                for(int col = 0; col < 5; col++){
                                    if(block.block[row][col] > 0){
                                        url = getClass().getResource("Sprites/Block"+block.block[row][col]+".png");
                                        ImageIcon icon = new ImageIcon(url);
                                        g.drawImage(icon.getImage(), 10+col*17+i*85, 324+row*17, 16, 16, null);
                                    }
                                }
                            }
                        }
                    }
                    
                    if(grabbedBlock != null){
                        URL url;
                        if(validPlacement){
                            dispBlock(grabbedBlock, (mouseX-4)/33, (mouseY-50)/33, false, false, g);
                        }
                        dispBlock(grabbedBlock, mouseX+16, mouseY+16, true, true, g);
                    }
                    
                    g.setColor(Color.WHITE);
                    if(gameOver && gameOverSpeed < 2){
                        g.drawString("Game Over", 70, 200);
                    }
                }
            }
        };
        
        panel.addMouseListener(new MouseListener(){
            @Override
            public void mousePressed(MouseEvent e){
                if(gameOver || clearLines){
                    return;
                }
                mouseX = e.getX();
                mouseY = e.getY();
                
                if(mouseY > 316){
                    for(int i = 0; i < 3; i++){
                        if(heldBlocks[i] != null && mouseX >= i*85-4 && mouseX < (i+1)*85){
                            placeGrabbed = i;
                            grabbedBlock = heldBlocks[i];
                            heldBlocks[i] = null;
                        }
                    }
                }else if((mouseX-4)/33 == playerX && (mouseY-50)/33 == playerY){
                    playerGrabbed = true;
                }
            }
            
            @Override
            public void mouseReleased(MouseEvent e){
                if(gameOver || clearLines){
                    return;
                }
                
                if(grabbedBlock != null){
                    if(validPlacement){
                        board = grabbedBlock.placeOn(board, (e.getX()-4)/33, (e.getY()-50)/33);
                        movesMade++;
                        if(gamemode == 3){
                            playerMoves++;
                            nextDrop--;
                        }
                        boolean noHeldBlocks = true;
                        for(int i = 0; i < 3; i++){
                            if(heldBlocks[i] != null){
                                noHeldBlocks = false;
                                break;
                            }
                        }
                        if(noHeldBlocks){
                            refreshHeldBlocks();
                        }
                        if(!clearLines){
                            checkGameOver();
                        }
                    }else{
                        heldBlocks[placeGrabbed] = grabbedBlock;
                    }
                    grabbedBlock = null;
                    validPlacement = false;
                }
                if(playerGrabbed){
                    if(validPlayerPlacement && playerMoves > 0){
                        playerX = (mouseX-4)/33;
                        playerY = (mouseY-50)/33;
                        playerMoves--;
                        nextDrop--;
                        checkGameOver();
                    }
                    playerGrabbed = false;
                    validPlayerPlacement = false;
                }
                
                panel.repaint();
            }
            
            @Override
            public void mouseClicked(MouseEvent e){
                if(gameOver){
                    gameOverSpeed++;
                }
            }
            
            @Override
            public void mouseEntered(MouseEvent e){}
            
            @Override
            public void mouseExited(MouseEvent e){}
        });
        panel.addMouseMotionListener(new MouseMotionListener(){
            @Override
            public void mouseMoved(MouseEvent e){}
            
            @Override
            public void mouseDragged(MouseEvent e){
                if(gameOver || clearLines){
                    return;
                }
                
                mouseX = e.getX();
                mouseY = e.getY();
                
                if(grabbedBlock != null){
                    if(gamemode == 3){
                        validPlacement = grabbedBlock.canBePlaced(board, (mouseX-4)/33, (mouseY-50)/33, playerX, playerY);
                    }else{
                        validPlacement = grabbedBlock.canBePlaced(board, (mouseX-4)/33, (mouseY-50)/33);
                    }
                }
                if(playerGrabbed){
                    int x = (mouseX-4)/33;
                    int y = (mouseY-50)/33;
                    if(x >= 0 &&
                        x < 8 &&
                        y >= 0 &&
                        y < 8 &&
                        Math.abs(playerX-x) < 2 &&
                        Math.abs(playerY-y) < 2 &&
                        board[y][x] == 0
                    ){
                        validPlayerPlacement = true;
                    }else{
                        validPlayerPlacement = false;
                    }
                }
                
                panel.repaint();
            }
        });
        panel.addKeyListener(new KeyListener(){
            @Override
            public void keyPressed(KeyEvent e){
                int key = e.getKeyCode();
                
                if(gameOver){
                    return;
                }
                
                if(clearLines){
                    return;
                }
                
                if(key == KeyEvent.VK_R){
                    gameOver = true;
                }
            }
            
            @Override
            public void keyReleased(KeyEvent e){}
            
            @Override
            public void keyTyped(KeyEvent e){}
        });
        
        panel.setSize(frameWidth, frameHeight);
        panel.setLayout(null);
        pane.add(panel, 0, 0);
        panel.setFocusable(true);
        
        
        b1 = new JButton("Standard");
        b1.setBounds(3, 210, 130, 100);
        b2 = new JButton("Chaos");
        b2.setBounds(138, 210, 130, 100);
        b3 = new JButton("Survival");
        b3.setBounds(3, 315, 130, 100);
        b4 = new JButton("Coming Soon");
        b4.setBounds(138, 315, 130, 100);
        home = new JButton("...");
        home.setBounds(240, 0, 32, 32);
        
        b1.addActionListener(new ActionListener(){
            @Override
            public void actionPerformed(ActionEvent e){
                gamemode = 1;
                updateMode = true;
                gameOver = true;
                gameOverSpeed = 2;
            }
        });
        b2.addActionListener(new ActionListener(){
            @Override
            public void actionPerformed(ActionEvent e){
                gamemode = 2;
                updateMode = true;
                gameOver = true;
                gameOverSpeed = 2;
            }
        });
        b3.addActionListener(new ActionListener(){
            @Override
            public void actionPerformed(ActionEvent e){
                gamemode = 3;
                updateMode = true;
                gameOver = true;
                gameOverSpeed = 2;
            }
        });
        b4.addActionListener(new ActionListener(){
            @Override
            public void actionPerformed(ActionEvent e){
                b4.setIcon(new ImageIcon(panel.getClass().getResource("Sprites/BlockSmiley.png")));
            }
        });
        home.addActionListener(new ActionListener(){
            @Override
            public void actionPerformed(ActionEvent e){
                if(gameOver){
                    return;
                }
                gamemode = 0;
                updateMode = true;
            }
        });
        
        panel.add(b1);
        panel.add(b2);
        panel.add(b3);
        panel.add(b4);
        
        
        
        frame.setSize(frameWidth, frameHeight);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);
        
        
        while(true){
            pause(20);
            panel.repaint();
            if(clearLines){
                clearLines();
            }
            if(nextDrop <= 0){
                dropBlocks();
            }
            if(updateMode){
                updateMode();
            }
            if(gameOver){
                restartGame();
            }
        }
        
        
    } //end of main
    
    public static void pause(int ms){
        try{Thread.sleep(ms);}catch(Exception e){}
    }
    
    public static void updateMode(){
        if(gamemode == 0){
            panel.removeAll();
            panel.add(b1);
            panel.add(b2);
            panel.add(b3);
            panel.add(b4);
            panel.repaint();
        }else{
            panel.removeAll();
            panel.add(home);
        }
        updateMode = false;
    }
    
    public static void refreshHeldBlocks(){
        for(int i = 0; i < 3; i++){
            if(gamemode == 1 || gamemode == 3){
                heldBlocks[i] = Constants.mainBlocks[rand.nextInt(Constants.mainBlocks.length)];
            }else if(gamemode == 2){
                if(rand.nextInt(5) > 0){
                    heldBlocks[i] = Constants.chaosBlocks[rand.nextInt(Constants.chaosBlocks.length)];
                }else{
                    int[][] block = new int[5][5];
                    for(int row = 0; row < 5; row++){
                        for(int col = 0; col < 5; col++){
                            if(rand.nextInt(2) == 0){
                                block[row][col] = rand.nextInt(8)+1;
                            }
                        }
                    }
                    heldBlocks[i] = new Block(block);
                }
            }else{
                heldBlocks[i] = Constants.mainBlocks[rand.nextInt(Constants.mainBlocks.length)];
            }
            heldBlocks[i].changeToColor(rand.nextInt(8)+1);
        }
    }
    public static void refreshDropBlocks(){
        for(int i = 0; i < movesMade/2+1 && i < dropBlocks.length; i++){
            dropBlocks[i] = Constants.mainBlocks[rand.nextInt(Constants.mainBlocks.length)];
            int x = rand.nextInt(8);
            int y = rand.nextInt(8);
            int accuracy = ((movesMade & 63)-rand.nextInt(25))/9;
            if(accuracy < 1){
                accuracy = 1;
            }
            dropBlocksX[i] = playerX+(playerX-x)/accuracy;
            dropBlocksY[i] = playerY+(playerY-y)/accuracy;
            dropBlocks[i].changeToColor(rand.nextInt(8)+1);
        }
    }
    
    public static void dropBlocks(){
        for(int i = 0; i < dropBlocks.length; i++){
            if(dropBlocks[i] != null){
                dropBlocks[i].placeOn(board, dropBlocksX[i], dropBlocksY[i]);
                checkClears(board, dropBlocksX[i], dropBlocksY[i]);
            }else{
                break;
            }
        }
        nextDrop = 3;
        if(board[playerY][playerX] > 0){
            gameOver = true;
            gameOverSpeed = 0;
        }else{
            refreshDropBlocks();
        }
        refreshHeldBlocks();
        checkGameOver();
    }
    
    public static void checkClears(int[][] board, int x, int y){
        for(int row = y-2; row <= y+2; row++){
            if(row >= 0 && row < 8){
                for(int col = 0; col < 8; col++){
                    if(board[row][col] == 0){
                        break;
                    }else if(col == 7){
                        clearRows[row] = true;
                        clearLines = true;
                    }
                }
            }
        }
        for(int col = x-2; col <= x+2; col++){
            if(col >= 0 && col < 8){
                for(int row = 0; row < 8; row++){
                    if(board[row][col] == 0){
                        break;
                    }else if(row == 7){
                        clearCols[col] = true;
                        clearLines = true;
                    }
                }
            }
        }
    }
    public static void clearLines(){
        for(int pos = 0; pos < 8; pos++){
            pause(50);
            for(int i = 0; i < 8; i++){
                if(clearRows[i]){
                    board[i][pos] = 0;
                }if(clearCols[i]){
                    board[pos][i] = 0;
                }
            }
            panel.repaint();
        }
        clearLines = false;
        clearRows = new boolean[8];
        clearCols = new boolean[8];
        checkGameOver();
    }
    
    public static void dispBlock(Block block, int xc, int yc, boolean actual, boolean opaque, Graphics2D g){
        for(int row = 0; row < 5; row++){
            for(int col = 0; col < 5; col++){
                int tile = block.block[row][col];
                if(tile > 0){
                    if(actual){
                        dispTile(tile, xc-80+col*32, yc-80+row*32, true, opaque, g);
                    }else{
                        dispTile(tile, xc+col-2, yc+row-2, false, opaque, g);
                    }
                }
            }
        }
    }
    public static void dispBlock(Block block, String image, int xc, int yc, boolean actual, boolean opaque, Graphics2D g){
        for(int row = 0; row < 5; row++){
            for(int col = 0; col < 5; col++){
                int tile = block.block[row][col];
                if(tile > 0){
                    if(actual){
                        dispTile(image, xc-80+col*32, yc-80+row*32, true, opaque, g);
                    }else{
                        dispTile(image, xc+col-2, yc+row-2, false, opaque, g);
                    }
                }
            }
        }
    }
    public static void dispTile(int tile, int xc, int yc, boolean actual, boolean opaque, Graphics2D g){
        if(tile != 0){
            dispTile("Block"+tile, xc, yc, actual, opaque, g);
        }else{
            int x;
            int y;
            if(actual){
                x = xc-16;
                y = yc-16;
            }else{
                if(xc < 0 || xc >= 8 || yc < 0 || yc >= 8){
                    return;
                }
                x = 5+xc*33;
                y = 51+yc*33;
            }
            g.setColor(Constants.bgColor);
            g.fillRect(x, y, 32, 32);
        }
    }
    public static void dispTile(String tile, int xc, int yc, boolean actual, boolean opaque, Graphics2D g){
        int x;
        int y;
        if(actual){
            x = xc-16;
            y = yc-16;
        }else{
            if(xc < 0 || xc >= 8 || yc < 0 || yc >= 8){
                return;
            }
            x = 5+xc*33;
            y = 51+yc*33;
        }
        URL url = panel.getClass().getResource("Sprites/"+tile+".png");
        ImageIcon icon = new ImageIcon(url);
        Image img = icon.getImage();
        BufferedImage image = new BufferedImage(img.getWidth(null), img.getHeight(null), BufferedImage.TYPE_INT_ARGB);
        
        //Draw the image on to the buffered image
        Graphics2D bGr = image.createGraphics();
        bGr.drawImage(img, 0, 0, null);
        bGr.dispose();
        
        if(!opaque){
            for(int i = 0; i < image.getWidth(); i++){
                for(int j = 0; j < image.getHeight(); j++){
                    image.setRGB(i, j, image.getRGB(i, j)+(100 << 24));
                }
            }
        }
        g.drawImage(image, x, y, 32, 32, null);
    }
    
    public static void checkGameOver(){
        if(gamemode == 3){
            //the player got crushed by a block
            if(board[playerY][playerX] > 0){
                gameOver = true;
                return;
            }
            if(playerMoves > 0){
                return;
            }
        }
        for(int i = 0; i < 3; i++){
            if(heldBlocks[i] != null){
                for(int x = 0; x < 8; x++){
                    for(int y = 0; y < 8; y++){
                        if(heldBlocks[i].canBePlaced(board, x, y)){
                            return;
                        }
                    }
                }
            }
        }
        gameOver = true;
    }
    
    public static void restartGame(){
        panel.repaint();
        if(gamemode > 0){
            for(int i = 0; i < 60000 && gameOverSpeed == 0; i++){
                pause(1);
            }
            heldBlocks = new Block[3];
            dropBlocks = new Block[5];
            for(int y = 0; y < 8; y++){
                for(int x = 0; x < 8; x++){
                    board[y][x] = rand.nextInt(8)+1;
                    if(gameOverSpeed <= 1){
                        panel.repaint();
                        pause(50);
                    }
                }
            }
            if(gameOverSpeed <= 1){
                pause(100);
            }
            for(int y = 0; y < 8; y++){
                for(int x = 0; x < 8; x++){
                    board[y][x] = 0;
                    if(gameOverSpeed <= 2){
                        panel.repaint();
                        pause(50);
                    }
                }
            }
        }else{
            heldBlocks = new Block[3];
            board = new int[8][8];
        }
        refreshHeldBlocks();
        movesMade = 0;
        if(gamemode == 3){
            refreshDropBlocks();
            nextDrop = 3;
            playerMoves = 3;
            playerX = 3;
            playerY = 3;
        }
        gameOver = false;
        gameOverSpeed = 0;
        panel.repaint();
    }
    
} //end of class Main

class Block{
    int[][] block = new int[5][5];
    Block(int[][] block){
        this.block = block;
    }
    void changeToColor(int color){
        for(int i = 0; i < 5; i++){
            for(int j = 0; j < 5; j++){
                if(block[i][j] != 0){
                    block[i][j] = color;
                }
            }
        }
    }
    boolean canBePlaced(int[][] board, int x, int y){
        if(x < 0 || x >= 8 || y < 0 || y >= 8){
            return false;
        }
        for(int i = 0; i < 5; i++){
            for(int j = 0; j < 5; j++){
                if(block[i][j] > 0){
                    int row = i-2+y;
                    int col = j-2+x;
                    if(row < 0 || row >= 8 || col < 0 || col >= 8 || board[row][col] > 0){
                        return false;
                    }
                }
            }
        }
        return true;
    }
    boolean canBePlaced(int[][] board, int x, int y, int playerX, int playerY){ //survival gamemode
        if(x < 0 || x >= 8 || y < 0 || y >= 8){
            return false;
        }
        for(int i = 0; i < 5; i++){
            for(int j = 0; j < 5; j++){
                if(block[i][j] > 0){
                    int row = i-2+y;
                    int col = j-2+x;
                    if(row < 0 || row >= 8 || col < 0 || col >= 8 || (col == playerX && row == playerY) || board[row][col] > 0){
                        return false;
                    }
                }
            }
        }
        return true;
    }
    int[][] placeOn(int[][] board, int x, int y){
        for(int i = 0; i < 5; i++){
            for(int j = 0; j < 5; j++){
                if(block[i][j] > 0){
                    int row = i-2+y;
                    int col = j-2+x;
                    if(row >= 0 && row < 8 && col >= 0 && col < 8 && (board[row][col] == 0 || Main.nextDrop <= 0)){
                        board[row][col] = block[i][j];
                    }else if(Main.nextDrop > 0){
                        throw new IllegalStateException("\nCannot place a block there\nProbably an error on my part");
                    }
                }
            }
        }
        Main.checkClears(board, x, y);
        return board;
    }
} //end of class Block


class Constants{
    static final Color bgColor = new Color(38, 32, 84);
    static final Color lowAlphaBGColor = new Color(38, 32, 84, 100);
    
    static final Block[] mainBlocks = {
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {1, 1, 1, 1, 1},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {1, 1, 1, 1, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 0, 0, 0}
        })
        /*
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        */
    };
    public static Block[] chaosBlocks = {
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {1, 1, 1, 1, 1},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 0, 1, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 1, 1},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 0, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 1},
            {0, 0, 0, 1, 1},
            {0, 0, 1, 1, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {1, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 1}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 1, 1},
            {0, 0, 0, 1, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {1, 1, 0, 1, 1},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {1, 1, 0, 0, 0},
            {1, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 0, 1},
            {0, 0, 0, 1, 1}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {1, 0, 0, 0, 0},
            {1, 0, 0, 0, 0},
            {1, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 1, 1},
            {0, 0, 0, 0, 1},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 1, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {1, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {1, 0, 1, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 1, 1, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 1, 1, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        new Block(new int[][]{
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 1, 0},
            {0, 0, 0, 0, 1}
        }),
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 1, 0, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 1, 0, 1, 0},
            {0, 0, 0, 0, 0}
        })
        /*
        new Block(new int[][]{
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0}
        }),
        */
    };
} //end of class Constants
