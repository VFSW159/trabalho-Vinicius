import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.chart.BarChart;
import javafx.scene.chart.CategoryAxis;
import javafx.scene.chart.NumberAxis;
import javafx.scene.chart.XYChart;
import javafx.scene.control.*;
import javafx.scene.layout.*;
import javafx.scene.paint.Color;
import javafx.scene.text.Font;
import javafx.scene.text.FontWeight;
import javafx.stage.Stage;

import java.util.HashMap;
import java.util.Map;

public class AppDashboard extends Application {

    private Stage primaryStage;
    
   

    @Override
    public void start(Stage primaryStage) {
        this.primaryStage = primaryStage;
        this.primaryStage.setTitle("Sistema Corporativo v1.0");

      

       
        mostrarTelaLogin();
        primaryStage.show();
    }

    private void mostrarTelaLogin() {
        VBox root = new VBox(15);
        root.setPadding(new Insets(40));
        root.setAlignment(Pos.CENTER);
        root.setStyle("-fx-background-color: #f4f6f9;");

        Label lblTitulo = new Label("Seja Bem-Vindo");
        lblTitulo.setFont(Font.font("Arial", FontWeight.BOLD, 24));
        lblTitulo.setTextFill(Color.web("#2c3e50"));

        TextField txtUsuario = new TextField();
        txtUsuario.setPromptText("Usuário");
        txtUsuario.setPrefHeight(35);

        PasswordField txtSenha = new PasswordField();
        txtSenha.setPromptText("Senha");
        txtSenha.setPrefHeight(35);

        Label lblMensagem = new Label();
        lblMensagem.setTextFill(Color.RED);

        Button btnLogin = new Button("Entrar");
        btnLogin.setPrefWidth(120);
        btnLogin.setPrefHeight(35);
        btnLogin.setStyle("-fx-background-color: #3498db; -fx-text-fill: white; -fx-font-weight: bold;");

        Button btnCadastrar = new Button("Criar Conta");
        btnCadastrar.setStyle("-fx-background-color: transparent; -fx-text-fill: #7f8c8d;");

       
        btnLogin.setOnAction(e -> {
            String user = txtUsuario.getText();
            String pass = txtSenha.getText();

            if (bancoUsuarios.containsKey(user) && bancoUsuarios.get(user).equals(pass)) {
                mostrarDashboard(user);
            } else {
                lblMensagem.setText("Usuário ou senha incorretos!");
            }
        });

       
        btnCadastrar.setOnAction(e -> {
            String user = txtUsuario.getText();
            String pass = txtSenha.getText();

            if (user.isEmpty() || pass.isEmpty()) {
                lblMensagem.setText("Preencha usuário e senha para cadastrar.");
            } else if (bancoUsuarios.containsKey(user)) {
                lblMensagem.setText("Usuário já existe!");
            } else {
                bancoUsuarios.put(user, pass);
                lblMensagem.setTextFill(Color.GREEN);
                lblMensagem.setText("Cadastro realizado com sucesso!");
                txtUsuario.clear();
                txtSenha.clear();
            }
        });

        root.getChildren().addAll(lblTitulo, txtUsuario, txtSenha, lblMensagem, btnLogin, btnCadastrar);
        
        Scene scene = new Scene(root, 400, 450);
        primaryStage.setScene(scene);
    }

   
    private void mostrarDashboard(String usuarioLogado) {
        BorderPane root = new BorderPane();

       
        VBox sidebar = new VBox(10);
        sidebar.setPadding(new Insets(20));
        sidebar.setPrefWidth(200);
        sidebar.setStyle("-fx-background-color: #2c3e50;");

        Label lblUser = new Label("Olá, " + usuarioLogado);
        lblUser.setTextFill(Color.WHITE);
        lblUser.setFont(Font.font("Arial", FontWeight.BOLD, 14));

        Button btnMenu1 = new Button("📊 Visão Geral");
        Button btnMenu2 = new Button("⚙️ Configurações");
        Button btnSair = new Button("🚪 Sair / Logout");

        
        String btnStyle = "-fx-background-color: transparent; -fx-text-fill: #ecf0f1; -fx-alignment: center-left; -fx-width: 180;";
        btnMenu1.setStyle(btnStyle + "-fx-font-weight: bold;");
        btnMenu2.setStyle(btnStyle);
        btnSair.setStyle("-fx-background-color: #e74c3c; -fx-text-fill: white;");
        btnSair.setPrefWidth(160);

        btnSair.setOnAction(e -> mostrarTelaLogin()); // Volta para o login

        sidebar.getChildren().addAll(lblUser, new Separator(), btnMenu1, btnMenu2, new Region(), btnSair);
        VBox.setVgrow(sidebar.getChildren().get(4), Priority.ALWAYS); // Joga o botão Sair para o rodapé da barra

        root.setLeft(sidebar);

      
        VBox conteudo = new VBox(20);
        conteudo.setPadding(new Insets(30));
        conteudo.setStyle("-fx-background-color: #ecf0f1;");

        Label lblPainel = new Label("Painel de Controle Financeiro");
        lblPainel.setFont(Font.font("Arial", FontWeight.BOLD, 22));

      
        HBox hboxCards = new HBox(20);
        
        VBox card1 = criarCardMétrica("Faturamento Total", "R$ 45.250,00", "#2ecc71");
        VBox card2 = criarCardMétrica("Novos Clientes", "+ 142", "#3498db");
        VBox card3 = criarCardMétrica("Despesas Mensais", "R$ 12.110,00", "#e74c3c");

        hboxCards.getChildren().addAll(card1, card2, card3);

        
        CategoryAxis xAxis = new CategoryAxis();
        NumberAxis yAxis = new NumberAxis();
        BarChart<String, Number> graficoBarras = new BarChart<>(xAxis, yAxis);
        graficoBarras.setTitle("Vendas por Categoria (Mês Atual)");
        xAxis.setLabel("Produtos");
        yAxis.setLabel("Valor (R$)");

        XYChart.Series<String, Number> series = new XYChart.Series<>();
        series.setName("Faturamento");
        series.getData().add(new XYChart.Data<>("Eletrônicos", 15000));
        series.getData().add(new XYChart.Data<>("Roupas", 9200));
        series.getData().add(new XYChart.Data<>("Eletrodomésticos", 12000));
        series.getData().add(new XYChart.Data<>("Livros", 4050));
        
        graficoBarras.getData().add(series);

        conteudo.getChildren().addAll(lblPainel, hboxCards, graficoBarras);
        root.setCenter(conteudo);

        Scene scene = new Scene(root, 1000, 650);
        primaryStage.setScene(scene);
    }

   
    private VBox criarCardMétrica(String titulo, String valor, String corHex) {
        VBox card = new VBox(5);
        card.setPadding(new Insets(15));
        card.setPrefWidth(220);
        card.setStyle("-fx-background-color: white; -fx-background-radius: 5; -fx-effect: dropshadow(three-pass-box, rgba(0,0,0,0.1), 10, 0, 0, 0);");

        Label lblT = new Label(titulo);
        lblT.setTextFill(Color.GRAY);
        lblT.setFont(Font.font("Arial", 12));

        Label lblV = new Label(valor);
        lblV.setFont(Font.font("Arial", FontWeight.BOLD, 18));
        lblV.setTextFill(Color.web(corHex));

        card.getChildren().addAll(lblT, lblV);
        return card;
    }

    public static void main(String[] args) {
        launch(args);
    }
}
