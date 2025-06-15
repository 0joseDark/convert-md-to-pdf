Here is the full **English translation** of the explanation and Python code for a Markdown-to-PDF converter using **PyQt5**, compatible with **Windows 10**, **Ubuntu**, and **macOS**.

---

### ✅ Required modules

Install them via terminal or command prompt:

```bash
pip install PyQt5 markdown pdfkit
```

> **Note**:
>
> * To generate PDFs with `pdfkit`, you must install **wkhtmltopdf**:
>
>   * Windows: [https://wkhtmltopdf.org/downloads.html](https://wkhtmltopdf.org/downloads.html) (.exe installer)
>   * Ubuntu: `sudo apt install wkhtmltopdf`
>   * macOS: `brew install wkhtmltopdf`

---

### 🖥️ Full Python code (step-by-step commented)

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

class MarkdownConverter(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Markdown to PDF Converter")
        self.setGeometry(100, 100, 500, 300)
        self.initUI()

    def initUI(self):
        layout = QVBoxLayout()

        # Menu bar
        menu_bar = QMenuBar()
        file_menu = menu_bar.addMenu("File")

        exit_action = QAction("Exit", self)
        exit_action.triggered.connect(self.close)
        file_menu.addAction(exit_action)

        layout.setMenuBar(menu_bar)

        # Buttons and labels
        self.lbl_source = QLabel("Source folder: Not selected")
        self.btn_source = QPushButton("Select source folder")
        self.btn_source.clicked.connect(self.select_source)

        self.lbl_dest = QLabel("Destination folder: Not selected")
        self.btn_dest = QPushButton("Select destination folder")
        self.btn_dest.clicked.connect(self.select_dest)

        self.btn_convert = QPushButton("Convert to PDF")
        self.btn_convert.clicked.connect(self.convert_files)

        # Progress bar
        self.progress_bar = QProgressBar()
        self.progress_bar.setValue(0)
        self.progress_bar.setAlignment(Qt.AlignCenter)

        # Add widgets to layout
        layout.addWidget(self.lbl_source)
        layout.addWidget(self.btn_source)
        layout.addWidget(self.lbl_dest)
        layout.addWidget(self.btn_dest)
        layout.addWidget(self.btn_convert)
        layout.addWidget(self.progress_bar)

        self.setLayout(layout)

        # Internal paths
        self.source_folder = ""
        self.dest_folder = ""

    def select_source(self):
        folder = QFileDialog.getExistingDirectory(self, "Select source folder")
        if folder:
            self.source_folder = folder
            self.lbl_source.setText(f"Source folder: {folder}")

    def select_dest(self):
        folder = QFileDialog.getExistingDirectory(self, "Select destination folder")
        if folder:
            self.dest_folder = folder
            self.lbl_dest.setText(f"Destination folder: {folder}")

    def convert_files(self):
        if not self.source_folder or not self.dest_folder:
            self.lbl_source.setText("⚠️ Please select both folders first!")
            return

        md_files = [f for f in os.listdir(self.source_folder) if f.endswith(".md")]
        total = len(md_files)
        if total == 0:
            self.lbl_source.setText("⚠️ No Markdown files found.")
            return

        for i, file in enumerate(md_files):
            md_path = os.path.join(self.source_folder, file)

            # Read markdown content
            with open(md_path, "r", encoding="utf-8") as f:
                md_content = f.read()
                html = markdown.markdown(md_content)

            # Create numbered filename
            pdf_name = f"{i+1:03d}_{os.path.splitext(file)[0]}.pdf"
            pdf_path = os.path.join(self.dest_folder, pdf_name)

            # Convert HTML to PDF
            try:
                pdfkit.from_string(html, pdf_path)
            except Exception as e:
                print(f"Error converting {file}: {e}")

            # Update progress bar
            progress = int(((i + 1) / total) * 100)
            self.progress_bar.setValue(progress)

        self.lbl_source.setText(f"✅ {total} files converted.")

# Run application
if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = MarkdownConverter()
    window.show()
    sys.exit(app.exec_())
```

---

### 📌 OS-specific instructions

#### ✔ Windows 10

* Install Python from [python.org](https://www.python.org/)
* Download and install `wkhtmltopdf.exe`, and ensure its path is added to the system `PATH` variable.

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

### 💡 Future improvements

* Add PDF preview
* Drag & drop support
* Convert to other formats (HTML, DOCX)
