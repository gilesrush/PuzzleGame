
package com.puzzle.ui;

import javax.swing.*;
        import javax.swing.border.BevelBorder;
import java.awt.*;
        import java.awt.event.*;
        import java.util.Random;

public class game extends JFrame implements KeyListener, ActionListener {
    int[][] data = new int[4][4];
    int x = 0, y = 0;
    int step = 0, elapsedTime = 0;
    Timer timer;
    Random r = new Random();
    int index, COUNT;
    int[][] win;
    String path;
    JMenuBar menuBar;
    JMenu function, aboutUs, picture;
    JMenuItem replay, loginAgain, Exit, Girls_picture, Animal_picture, Sports_picture, record, accountItem, helpItem;

    public game() {
        index = r.nextInt(1, 4);
        COUNT = 0;
        win = new int[][]{{1, 2, 3, 4}, {5, 6, 7, 8}, {9, 10, 11, 12}, {13, 14, 15, 0}};

        function = new JMenu("機能");
        aboutUs = new JMenu("連絡先");
        picture = new JMenu("画像変更");

        replay = new JMenuItem("再挑戦");
        loginAgain = new JMenuItem("ログイン画面へ");
        Exit = new JMenuItem("終了");
        Girls_picture = new JMenuItem("女の子");
        Animal_picture = new JMenuItem("動物");
        Sports_picture = new JMenuItem("スポーツ");
        record = new JMenuItem("記録");
        accountItem = new JMenuItem("お問い合わせ");
        helpItem = new JMenuItem("説明");

        helpItem.addActionListener(e -> showHelpDialog());

        initJFrame();
        initMenuBar();
        initData();
        initImage();
        startTimer();
        setVisible(true);
    }

    private void initData() {
        int[] tempArr = new int[16];
        for (int i = 0; i < tempArr.length; i++) tempArr[i] = i;

        for (int i = 0; i < tempArr.length; i++) {
            int idx = r.nextInt(tempArr.length);
            int temp = tempArr[i];
            tempArr[i] = tempArr[idx];
            tempArr[idx] = temp;
        }

        for (int i = 0; i < tempArr.length; i++) {
            data[i / 4][i % 4] = tempArr[i];
            if (tempArr[i] == 0) {
                x = i / 4;
                y = i % 4;
            }
        }
    }

    private void initImage() {
        startPicture();
        getContentPane().removeAll();

        if (victory()) {
            timer.stop();
            JLabel winLabel = new JLabel(new ImageIcon("src/image/win.png"));
            winLabel.setBounds(203, 283, 197, 73);
            getContentPane().add(winLabel);
        }

        JLabel stepLabel = new JLabel("ステップ数: " + step);
        stepLabel.setBounds(50, 30, 150, 20);
        getContentPane().add(stepLabel);

        for (int i = 0; i < 4; i++) {
            for (int j = 0; j < 4; j++) {
                int num = data[i][j];
                String imagePath = path + num + ".jpg";
                JLabel label = new JLabel(new ImageIcon(imagePath));
                label.setBounds(105 * j + 83, 105 * i + 134, 105, 105);
                label.setBorder(new BevelBorder(BevelBorder.RAISED));
                getContentPane().add(label);
            }
        }

        JLabel bg = new JLabel(new ImageIcon("src/image/background.png"));
        bg.setBounds(40, 40, 508, 560);
        getContentPane().add(bg);
        getContentPane().repaint();
    }

    private void startPicture() {
        if (COUNT == 0 && index == 1) {
            int idx = r.nextInt(1, 14);
            path = "src/image/girl/girl" + idx + "/";
            COUNT++;
        } else if (COUNT == 0 && index == 2) {
            int idx = r.nextInt(1, 9);
            path = "src/image/animal/animal" + idx + "/";
            COUNT++;
        } else if (COUNT == 0 && index == 3) {
            int idx = r.nextInt(1, 11);
            path = "src/image/sport/sport" + idx + "/";
            COUNT++;
        }
    }

    private void initJFrame() {
        setSize(603, 680);
        setTitle("パズルゲーム v1.0");
        setAlwaysOnTop(true);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(null);
        addKeyListener(this);
    }

    private void initMenuBar() {
        menuBar = new JMenuBar();

        function.add(replay);
        function.add(loginAgain);
        function.add(Exit);
        function.add(picture);
        function.add(record);

        picture.add(Girls_picture);
        picture.add(Animal_picture);
        picture.add(Sports_picture);

        aboutUs.add(accountItem);

        JMenu helpMenu = new JMenu("ヘルプ");
        helpMenu.add(helpItem);

        menuBar.add(function);
        menuBar.add(aboutUs);
        menuBar.add(helpMenu);

        setJMenuBar(menuBar);

        replay.addActionListener(this);
        loginAgain.addActionListener(this);
        Exit.addActionListener(this);
        Girls_picture.addActionListener(this);
        Animal_picture.addActionListener(this);
        Sports_picture.addActionListener(this);
        record.addActionListener(this);
        accountItem.addActionListener(this);
    }

    private void startTimer() {
        timer = new Timer(1000, e -> setTitle("パズルゲーム v1.0 - 経過時間：" + (++elapsedTime) + "秒"));
        timer.start();
    }

    private void showHelpDialog() {
        String helpText = "<html><body style='width:300px;'>"
                + "<h2>ゲーム説明</h2>"
                + "<p>方向キーでパネルをスライド。Aキーで完成図、Wキーで自動完成。</p>"
                + "<p>すべて揃えると勝利！</p></body></html>";
        JLabel label = new JLabel(helpText);
        label.setFont(new Font("Dialog", Font.PLAIN, 14));
        JOptionPane.showMessageDialog(this, label, "ゲーム説明", JOptionPane.INFORMATION_MESSAGE);
    }

    public void keyTyped(KeyEvent e) {}

    public void keyPressed(KeyEvent e) {
        if (e.getKeyCode() == KeyEvent.VK_A) {
            getContentPane().removeAll();
            JLabel all = new JLabel(new ImageIcon(path + "all.jpg"));
            all.setBounds(83, 134, 420, 420);
            getContentPane().add(all);
            JLabel bg = new JLabel(new ImageIcon("src/image/background.png"));
            bg.setBounds(40, 40, 508, 560);
            getContentPane().add(bg);
            getContentPane().repaint();
        }
    }

    public void keyReleased(KeyEvent e) {
        if (!victory()) {
            int code = e.getKeyCode();
            if (code == KeyEvent.VK_LEFT && y < 3) {
                data[x][y] = data[x][y + 1];
                data[x][y + 1] = 0;
                y++;
                step++;
            } else if (code == KeyEvent.VK_UP && x < 3) {
                data[x][y] = data[x + 1][y];
                data[x + 1][y] = 0;
                x++;
                step++;
            } else if (code == KeyEvent.VK_RIGHT && y > 0) {
                data[x][y] = data[x][y - 1];
                data[x][y - 1] = 0;
                y--;
                step++;
            } else if (code == KeyEvent.VK_DOWN && x > 0) {
                data[x][y] = data[x - 1][y];
                data[x - 1][y] = 0;
                x--;
                step++;
            } else if (code == KeyEvent.VK_A || code == KeyEvent.VK_W) {
                data = new int[][]{
                        {1, 2, 3, 4},
                        {5, 6, 7, 8},
                        {9, 10, 11, 12},
                        {13, 14, 15, 0}};
                step++;
            }
            initImage();
        }
    }

    public boolean victory() {
        for (int i = 0; i < 4; i++)
            for (int j = 0; j < 4; j++)
                if (data[i][j] != win[i][j])
                    return false;
        return true;
    }

    public void actionPerformed(ActionEvent e) {
        Object source = e.getSource();
        if (source == replay) resetGame();
        else if (source == loginAgain) {
            setVisible(false);
            timer.stop();
            new login();
        } else if (source == Exit) {
            timer.stop();
            System.exit(0);
        } else if (source == Girls_picture) {
            path = "src/image/girl/girl" + r.nextInt(1, 14) + "/";
            resetGame();
        } else if (source == Animal_picture) {
            path = "src/image/animal/animal" + r.nextInt(1, 9) + "/";
            resetGame();
        } else if (source == Sports_picture) {
            path = "src/image/sport/sport" + r.nextInt(1, 11) + "/";
            resetGame();
        } else if (source == record) {
            JDialog dialog = new JDialog(this, "記録", true);
            JLabel label = new JLabel("ステップ数: " + step + "、時間: " + elapsedTime + "秒", JLabel.CENTER);
            label.setFont(new Font("MS Gothic", Font.PLAIN, 16));
            dialog.add(label);
            dialog.setSize(300, 150);
            dialog.setLocationRelativeTo(this);
            dialog.setVisible(true);
        } else if (source == accountItem) {
            JDialog dialog = new JDialog(this, "お問い合わせ", true);
            String message = "<html><center>ご質問があれば、<br>080-1357-9682<br>ctmn0008@mail4.doshisha.ac.jp<br>同志社大学 李 佳明</center></html>";
            JLabel label = new JLabel(message, JLabel.CENTER);
            label.setFont(new Font("Arial", Font.PLAIN, 14));
            dialog.add(label);
            dialog.setSize(360, 200);
            dialog.setLocationRelativeTo(this);
            dialog.setVisible(true);
        }
        initImage();
    }

    private void resetGame() {
        step = 0;
        elapsedTime = 0;
        timer.restart();
        initData();
        initImage();
    }
}













