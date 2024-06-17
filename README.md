import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

// Classe base para os upgrades
class Upgr {
    protected int bonus;
    protected String nome;
    protected int quantidade;
    protected int custo;

    public Upgr(String nome, int bonus, int custoInicial) {
        this.nome = nome;
        this.bonus = bonus;
        this.quantidade = 0;
        this.custo = custoInicial;
    }

    public int getBonus() {
        return bonus;
    }

    public String getNome() {
        return nome;
    }

    public int getQuantidade() {
        return quantidade;
    }

    public int getCusto() {
        return custo;
    }

    // Método para comprar o upgrade
    public void comprar() {
        quantidade++;
        custo += custo / 2; // Aumenta o custo em 50% a cada compra
    }

    // Método para calcular a produção por segundo deste upgrade
    public int calcularProdPorSegundo() {
        return bonus * quantidade;
    }
}

// Classe para o upgrade de Ferro
class UpgrFerro extends Upgr {
    public UpgrFerro() {
        super("Upgrade de Ferro", 1, 50); // Bônus de 1 minério de ferro por segundo, custo inicial de 50
    }
}

// Classe para o upgrade de Ouro
class UpgrOuro extends Upgr {
    public UpgrOuro() {
        super("Upgrade de Ouro", 2, 100); // Bônus de 2 minérios de ouro por segundo, custo inicial de 100
    }
}

// Classe para o upgrade de Diamante
class UpgrDiamante extends Upgr {
    public UpgrDiamante() {
        super("Upgrade de Diamante", 3, 150); // Bônus de 3 diamantes por segundo, custo inicial de 150
    }
}

// Classe principal do Miners Clicker
public class MinersClicker extends JPanel implements ActionListener {
    private int minerais = 0; // Contador de minerais
    private JLabel displayMinerais; // Label para exibir a quantidade de minerais
    private JLabel[] displayQuantUpgr; // Array de labels para exibir a quantidade de cada upgrade
    private Upgr[] upgrades; // Array de upgrades disponíveis
    private JButton botaoClicar; // Botão principal para clicar e minerar
    private JButton[] botoesUpgr; // Array de botões para comprar upgrades
    private Timer timer; // Timer para incrementar automaticamente os minerais

    public MinersClicker() {
        setLayout(new BorderLayout()); // Define o layout do painel principal
        inicializarComponentes(); 
        inicializarTimer(); 
    }

    // Método para inicializar os componentes da interface gráfica
    private void inicializarComponentes() {

        // Label para exibir a quantidade de minerais
        displayMinerais = new JLabel("Minerais: " + minerais);
        add(displayMinerais, BorderLayout.NORTH);

        // Botão para clicar e adicionar minerais
        botaoClicar = new JButton("Clicar para Minerar");
        botaoClicar.setPreferredSize(new Dimension(300, 300));
        botaoClicar.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                minerais++; // Adiciona 1 mineral por clique
                atualizarDisplay(); 
            }
        });
        add(botaoClicar, BorderLayout.CENTER);

        // Painel para os botões de upgrade
        JPanel panelUpgr = new JPanel(new GridLayout(0, 1));

        // Inicializa os upgrades
        upgrades = new Upgr[]{new UpgrFerro(), new UpgrOuro(), new UpgrDiamante()};

        // Inicializa os botões de upgrade e seus labels
        botoesUpgr = new JButton[upgrades.length];
        displayQuantUpgr = new JLabel[upgrades.length];

        for (int i = 0; i < upgrades.length; i++) {
            final int index = i; // Índice atual
            botoesUpgr[i] = new JButton("Comprar " + upgrades[i].getNome() + " (+" + upgrades[i].getBonus() + "/s) - Custo: " + upgrades[i].getCusto());
            botoesUpgr[i].setPreferredSize(new Dimension(200, 50));
            botoesUpgr[i].addActionListener(new ActionListener() {
                public void actionPerformed(ActionEvent e) {
                    comprarUpgr(upgrades[index]); // Compra o upgrade correspondente
                }
            });
            panelUpgr.add(botoesUpgr[i]);

            // Inicializa os labels de quantidade de upgrades
            displayQuantUpgr[i] = new JLabel(upgrades[i].getNome() + ": " + upgrades[i].getQuantidade());
            panelUpgr.add(displayQuantUpgr[i]);
        }

        // Adiciona o painel de upgrades ao painel principal
        add(panelUpgr, BorderLayout.SOUTH);
    }

    // Método para inicializar o timer
    private void inicializarTimer() {
        timer = new Timer(1000, new ActionListener() { // Timer dispara a cada 1000ms (1 segundo)
            public void actionPerformed(ActionEvent e) {
                increMineraisAutomatico(); 
                atualizarDisplay(); 
            }
        });
        timer.start(); // Inicia o timer
    }

    // Método para incrementar a quantidade de minerais automaticamente
    private void increMineraisAutomatico() {
        for (Upgr upgrade : upgrades) {
            minerais += upgrade.calcularProdPorSegundo(); // Adiciona a produção por segundo de cada upgrade
        }
    }

    // Método para atualizar o display de minerais e de quantidade de upgrades
    private void atualizarDisplay() {
        displayMinerais.setText("Minerais: " + minerais); // Atualiza o display de minerais
        for (int i = 0; i < upgrades.length; i++) {
            displayQuantUpgr[i].setText(upgrades[i].getNome() + ": " + upgrades[i].getQuantidade()); // Atualiza a quantidade de upgrades
            botoesUpgr[i].setText("Comprar " + upgrades[i].getNome() + " (+" + upgrades[i].getBonus() + "/s) - Custo: " + upgrades[i].getCusto()); // Atualiza o texto do botão de upgrade
        }
    }

    // Método para comprar um upgrade
    private void comprarUpgr(Upgr upgrade) {
        if (minerais >= upgrade.getCusto()) { // Verifica se há minerais suficientes
            minerais -= upgrade.getCusto(); // Deduz o custo do upgrade
            upgrade.comprar(); // Compra o upgrade
            atualizarDisplay(); // Atualiza os displays
            JOptionPane.showMessageDialog(this, upgrade.getNome() + " comprado!"); // Exibe mensagem de confirmação
        } else {
            JOptionPane.showMessageDialog(this, "Você não tem minerais suficientes para comprar o upgrade!"); // Exibe mensagem de erro
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                JFrame frame = new JFrame("Miners Clicker"); // Cria a janela principal
                frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // Define a ação de fechar a janela
                MinersClicker minersClicker = new MinersClicker(); // Cria uma instância do jogo
                frame.getContentPane().add(minersClicker); // Adiciona o jogo à janela
                frame.setSize(500, 600); // Define o tamanho da janela
                frame.setVisible(true); // Torna a janela visível
            }
        });
    }

    // Método para tratar eventos dos botões
    public void actionPerformed(ActionEvent e) {
        // Este método pode ser usado se houver mais botões ou eventos a serem tratados
    }
}
