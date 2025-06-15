Vamos criar uma aplicação multiplataforma (Windows 10, Ubuntu e macOS) em **Python** usando **PyQt5**, que permite:

* Escolher uma pasta com ficheiros `.md` (Markdown)
* Escolher uma pasta de destino para guardar os ficheiros PDF
* Converter cada ficheiro `.md` para `.pdf`
* Numerar os PDFs
* Mostrar uma barra de progresso com percentagem
* Ter um menu e botões intuitivos

---

### ✅ Módulos necessários

Instala no terminal (cmd/powershell/terminal bash):

```bash
pip install PyQt5 markdown pdfkit
```

> **Nota**:
>
> * Para gerar PDF com `pdfkit`, deves ter o **wkhtmltopdf** instalado:
>
>   * Windows: [https://wkhtmltopdf.org/downloads.html](https://wkhtmltopdf.org/downloads.html) (escolhe o `.exe`)
>   * Ubuntu: `sudo apt install wkhtmltopdf`
>   * macOS: `brew install wkhtmltopdf`

---

### 🖥️ Código Python completo (comentado)

```python
import sys
import os
import markdown
import pdfkit
from PyQt5.QtWidgets import (
    QApplication, QWidget, QVBoxLayout, QPushButton, QLabel,
    QFileDialog, QProgressBar, QMenuBar, QAction
)
from PyQt5.QtCore import Qt

class ConversorMarkdown(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Conversor Markdown para PDF")
        self.setGeometry(100, 100, 500, 300)
        self.initUI()

    def initUI(self):
        layout = QVBoxLayout()

        # Barra de menu
        menu_bar = QMenuBar()
        ficheiro_menu = menu_bar.addMenu("Ficheiro")

        sair_acao = QAction("Sair", self)
        sair_acao.triggered.connect(self.close)
        ficheiro_menu.addAction(sair_acao)

        layout.setMenuBar(menu_bar)

        # Botões
        self.lbl_origem = QLabel("Pasta de origem: Não selecionada")
        self.btn_origem = QPushButton("Selecionar pasta de origem")
        self.btn_origem.clicked.connect(self.selecionar_origem)

        self.lbl_destino = QLabel("Pasta de destino: Não selecionada")
        self.btn_destino = QPushButton("Selecionar pasta de destino")
        self.btn_destino.clicked.connect(self.selecionar_destino)

        self.btn_converter = QPushButton("Converter para PDF")
        self.btn_converter.clicked.connect(self.converter_ficheiros)

        # Barra de progresso
        self.progress_bar = QProgressBar()
        self.progress_bar.setValue(0)
        self.progress_bar.setAlignment(Qt.AlignCenter)

        # Adiciona ao layout
        layout.addWidget(self.lbl_origem)
        layout.addWidget(self.btn_origem)
        layout.addWidget(self.lbl_destino)
        layout.addWidget(self.btn_destino)
        layout.addWidget(self.btn_converter)
        layout.addWidget(self.progress_bar)

        self.setLayout(layout)

        # Variáveis internas
        self.pasta_origem = ""
        self.pasta_destino = ""

    def selecionar_origem(self):
        pasta = QFileDialog.getExistingDirectory(self, "Selecionar pasta de origem")
        if pasta:
            self.pasta_origem = pasta
            self.lbl_origem.setText(f"Pasta de origem: {pasta}")

    def selecionar_destino(self):
        pasta = QFileDialog.getExistingDirectory(self, "Selecionar pasta de destino")
        if pasta:
            self.pasta_destino = pasta
            self.lbl_destino.setText(f"Pasta de destino: {pasta}")

    def converter_ficheiros(self):
        if not self.pasta_origem or not self.pasta_destino:
            self.lbl_origem.setText("⚠️ Seleciona as pastas primeiro!")
            return

        ficheiros_md = [f for f in os.listdir(self.pasta_origem) if f.endswith(".md")]
        total = len(ficheiros_md)
        if total == 0:
            self.lbl_origem.setText("⚠️ Nenhum ficheiro Markdown encontrado.")
            return

        for i, ficheiro in enumerate(ficheiros_md):
            caminho_md = os.path.join(self.pasta_origem, ficheiro)

            # Lê o conteúdo Markdown
            with open(caminho_md, "r", encoding="utf-8") as f:
                conteudo_md = f.read()
                html = markdown.markdown(conteudo_md)

            # Define o nome numerado
            nome_pdf = f"{i+1:03d}_{os.path.splitext(ficheiro)[0]}.pdf"
            caminho_pdf = os.path.join(self.pasta_destino, nome_pdf)

            # Converte HTML para PDF
            try:
                pdfkit.from_string(html, caminho_pdf)
            except Exception as e:
                print(f"Erro ao converter {ficheiro}: {e}")

            # Atualiza a barra de progresso
            progresso = int(((i + 1) / total) * 100)
            self.progress_bar.setValue(progresso)

        self.lbl_origem.setText(f"✅ {total} ficheiros convertidos.")

# Execução da aplicação
if __name__ == "__main__":
    app = QApplication(sys.argv)
    janela = ConversorMarkdown()
    janela.show()
    sys.exit(app.exec_())
```

---

### 📌 Explicação por sistema operativo

#### ✔ Windows 10

* Instala Python via [python.org](https://www.python.org/)
* Instala o `wkhtmltopdf.exe` e certifica-te de que o caminho está na variável de ambiente `PATH`.

#### ✔ Ubuntu

```bash
sudo apt install python3 python3-pip wkhtmltopdf
pip3 install PyQt5 markdown pdfkit
```

#### ✔ macOS

```bash
brew install python wkhtmltopdf
pip3 install PyQt5 markdown pdfkit
```

---

### 💡 Sugestões futuras

* Adicionar visualização do PDF gerado
* Suporte a arrastar ficheiros
* Converter para outros formatos (HTML, DOCX)
