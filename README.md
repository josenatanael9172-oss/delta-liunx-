<meta name="google-site-verification" content="4gvWc2Xk7Tx4SkWkDUmhTEs8NMvE-COqrXHU3V9udcI" />🚀 Delta Linux

O **Delta Linux** é um sistema operacional de código aberto, minimalista e altamente performático, construído com base nas tecnologias robustas do ecossistema Fedora. O projeto foca em leveza, transparência visual e produtividade fluida.

---

## ✨ Principais Características

- **Interface Própria em C++:** Desenvolvida do zero utilizando **Qt6 / CMake**, trazendo janelas fluidas e elementos visuais modernos inspirados na estética do KDE Plasma.
- **Barra de Pesquisa Flutuante:** Um inicializador centralizado e rápido (estilo KRunner/VR) para buscar aplicativos e disparar comandos instantaneamente com a tecla de atalho.
- **Base Sólida:** Empacotado sob a confiabilidade e segurança dos pacotes e ferramentas de compilação do Fedora.
- **Open Source:** Totalmente aberto à comunidade para contribuições, melhorias e novas ideias.

---

## 🛠️ Tecnologias Utilizadas

- **Linguagem:** C++ (Padrão C++17)
- **Framework Gráfico:** Qt6 (Core, Gui, Widgets)
- **Build System:** CMake
- **Instalação/ISO:** Automação via arquivos Kickstart do Fedora.

---

## 💻 Como Compilar a Interface Manualmente

Se você quiser testar o código da interface gráfica no seu ambiente Linux, siga os passos abaixo:

```bash
# Clone o repositório
git clone [https://github.com/SEU-USUARIO/delta-linux.git](https://github.com/SEU-USUARIO/delta-linux.git)
cd delta-linux

# Crie a pasta de build
mkdir build && cd build

# Configure com o CMake e compile
cmake ..
make

# Execute a interface
./delta-ui
