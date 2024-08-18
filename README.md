import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

// Main class to set up the game window
public class Game {
    public static void main(String[] args) {
        JFrame frame = new JFrame("Gun Game");
        GamePanel gamePanel = new GamePanel();

        frame.add(gamePanel);
        frame.setSize(800, 600);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);

        gamePanel.startGame();
    }
}

// Panel that handles game drawing and updates
class GamePanel extends JPanel implements ActionListener, KeyListener {
    private Timer timer;
    private Player player;

    public GamePanel() {
        player = new Player();
        timer = new Timer(16, this); // Approximately 60 FPS
        addKeyListener(this);
        setFocusable(true); // Ensure the panel can receive key events
        requestFocusInWindow(); // Request focus for the panel
    }

    public void startGame() {
        timer.start();
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        if (player != null) {
            player.draw(g);
            for (Bullet bullet : player.getBullets()) {
                bullet.draw(g);
            }
        } else {
            System.out.println("Player is null in paintComponent");
        }
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (player != null) {
            player.update();
            for (Bullet bullet : player.getBullets()) {
                bullet.update();
            }
            repaint();
        } else {
            System.out.println("Player is null in actionPerformed");
        }
    }

    @Override
    public void keyPressed(KeyEvent e) {
        if (player != null) {
            player.keyPressed(e);
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {
        if (player != null) {
            player.keyReleased(e);
        }
    }

    @Override
    public void keyTyped(KeyEvent e) {}
}

// Player class handling the player logic
class Player {
    private int x, y;
    private int speed = 5;
    private ArrayList<Bullet> bullets;

    // Movement directions
    private boolean movingLeft, movingRight, movingUp, movingDown;

    public Player() {
        this.x = 400; // Start in the middle of the screen
        this.y = 300;
        bullets = new ArrayList<>();
    }

    public void draw(Graphics g) {
        g.setColor(Color.BLUE);
        g.fillRect(x, y, 50, 50); // Draw the player as a blue square
        for (Bullet bullet : bullets) {
            bullet.draw(g);
        }
    }

    public void update() {
        // Update player position based on movement
        if (movingLeft) x -= speed;
        if (movingRight) x += speed;
        if (movingUp) y -= speed;
        if (movingDown) y += speed;

        // Prevent the player from going out of bounds
        x = Math.max(0, Math.min(x, 750)); // Assuming player width is 50
        y = Math.max(0, Math.min(y, 550)); // Assuming player height is 50
    }

    public void shoot() {
        bullets.add(new Bullet(x + 22, y)); // Spawn bullet at player's position
    }

    public ArrayList<Bullet> getBullets() {
        return bullets;
    }

    public void keyPressed(KeyEvent e) {
        switch (e.getKeyCode()) {
            case KeyEvent.VK_SPACE:
                shoot();
                break;
            case KeyEvent.VK_LEFT:
                movingLeft = true;
                break;
            case KeyEvent.VK_RIGHT:
                movingRight = true;
                break;
            case KeyEvent.VK_UP:
                movingUp = true;
                break;
            case KeyEvent.VK_DOWN:
                movingDown = true;
                break;
        }
    }

    public void keyReleased(KeyEvent e) {
        switch (e.getKeyCode()) {
            case KeyEvent.VK_LEFT:
                movingLeft = false;
                break;
            case KeyEvent.VK_RIGHT:
                movingRight = false;
                break;
            case KeyEvent.VK_UP:
                movingUp = false;
                break;
            case KeyEvent.VK_DOWN:
                movingDown = false;
                break;
        }
    }
}

// Bullet class handling the bullet logic
class Bullet {
    private int x, y;
    private int speed = 10;

    public Bullet(int startX, int startY) {
        this.x = startX;
        this.y = startY;
    }

    public void update() {
        y -= speed; // Move the bullet upwards
    }

    public void draw(Graphics g) {
        g.setColor(Color.RED);
        g.fillOval(x, y, 5, 10); // Draw the bullet as a red oval
    }
}
