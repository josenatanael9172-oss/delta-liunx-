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
esse é o código do Delta os url --url=https://download.fedoraproject.org/pub/fedora/linux/releases/42/Everything/x86_64/os/
network --bootproto=dhcp --device=link --activate
firewall --disabled
selinux --disabled
rootpw --plaintext senha123
timezone America/Sao_Paulo --utc
bootloader --location=mbr --append="quiet splash"

zerombr
clearpart --all --initlabel
part / --fstype=ext4 --size=6000 --grow

%packages
dracut-live
@xfce-desktop
lightdm
lightdm-gtk-greeter
grub2-efi
grub2-pc
zenity
xterm
%end

%post
# --- PÓS-INSTALAÇÃO: DELTA LINUX COMPLETO ---
echo "A aplicar a configuração completa do Delta Linux..."

# 1. Criar pastas de sistema necessárias
mkdir -p /usr/share/backgrounds/
SKEL_CONFIG="/etc/skel/.config"
mkdir -p "$SKEL_CONFIG/xfce4/xfconf/xfce-perchannel-xml"
mkdir -p /usr/share/xsessions
mkdir -p /usr/share/applications

# 2. Configurar o fundo de ecrã no LightDM-GTK e definir a sessão padrão
if [ -f /etc/lightdm/lightdm-gtk-greeter.conf ]; then
    sed -i 's/^#background=.*/background=\/usr\/share\/backgrounds\/delta_linux_bg.png/' /etc/lightdm/lightdm-gtk-greeter.conf
fi

if [ -f /etc/lightdm/lightdm.conf ]; then
    sed -i 's/^#user-session=.*/user-session=delta-xfce/' /etc/lightdm/lightdm.conf
else
    echo -e "[Seat:*]\nuser-session=delta-xfce" > /etc/lightdm/lightdm.conf
fi

# 3. Anunciar a sessão gráfica oficial do Delta Linux
cat << 'EOF' > /usr/share/xsessions/delta-xfce.desktop
[Desktop Entry]
Name=Delta Linux XFCE
Comment=Ambiente gráfico oficial do Delta Linux baseado em XFCE
Exec=startxfce4
TryExec=startxfce4
DesktopNames=XFCE
Type=Application
EOF

# 4. Configurar o tema visual e definições gerais do XFCE para novos utilizadores
cat << 'EOF' > "$SKEL_CONFIG/xfce4/xfconf/xfce-perchannel-xml/xsettings.xml"
<?xml version="1.0" encoding="UTF-8"?>
<channel name="xsettings" version="1.0">
  <property name="Net" type="empty">
    <property name="ThemeName" type="string" value="Adwaita-dark"/>
    <property name="IconThemeName" type="string" value="Adwaita"/>
    <property name="DoubleClickTimeout" type="int" value="250"/>
  </property>
</channel>
EOF

# 5. Definir o fundo de ecrã personalizado do Delta Linux no XFCE
cat << 'EOF' > "$SKEL_CONFIG/xfce4/xfconf/xfce-perchannel-xml/xfce4-desktop.xml"
<?xml version="1.0" encoding="UTF-8"?>
<channel name="xfce4-desktop" version="1.0">
  <property name="backdrop" type="empty">
    <property name="screen0" type="empty">
      <property name="monitor0" type="empty">
        <property name="image-path" type="string" value="/usr/share/backgrounds/delta_linux_bg.png"/>
        <property name="style" type="int" value="5"/>
      </property>
    </property>
  </property>
</channel>
EOF

# 6. Configurar o painel do XFCE personalizado
cat << 'EOF' > "$SKEL_CONFIG/xfce4/xfconf/xfce-perchannel-xml/xfce4-panel.xml"
<?xml version="1.0" encoding="UTF-8"?>
<channel name="xfce4-panel" version="1.0">
  <property name="configversion" type="int" value="2"/>
  <property name="panels" type="array">
    <property name="panel-0" type="empty">
      <property name="position" type="string" value="p=6;x=0;y=0"/>
      <property name="length" type="uint" value="100"/>
      <property name="position-locked" type="bool" value="true"/>
      <property name="size" type="uint" value="28"/>
      <property name="plugins" type="array">
        <property name="plugin-1" type="string" value="applicationsmenu"/>
        <property name="plugin-2" type="string" value="tasklist"/>
        <property name="plugin-3" type="string" value="separator">
          <property name="expand" type="bool" value="true"/>
        </property>
        <property name="plugin-4" type="string" value="systray"/>
        <property name="plugin-5" type="string" value="clock"/>
        <property name="plugin-6" type="string" value="actions"/>
      </property>
    </property>
  </property>
</channel>
EOF

# 7. Criar a Central de Controlo do Delta Linux
cat << 'EOF' > /usr/local/bin/delta-control
#!/bin/bash
if ! command -v zenity &> /dev/null; then
    echo "=== Central do Delta Linux ==="
    echo "1. Informações do Sistema"
    echo "2. Atualizar Sistema"
    echo "3. Alterar Fundo de Ecrã"
    echo "4. Sair"
    exit 0
fi

while true; do
    escolha=$(zenity --list --title="Central do Delta Linux" \
        --text="Bem-vindo à Central de Controlo do Delta Linux!\nSelecione uma opção:" \
        --column="Nº" --column="Ação / Ferramenta" \
        1 "Ver Informações do Sistema" \
        2 "Abrir Gestor de Ficheiros" \
        3 "Configurações de Ecrã" \
        4 "Repositório Oficial do Projeto" \
        5 "Sair")

    case $escolha in
        1)
            info="Sistema: Delta Linux (Base Fedora)\nAmbiente: XFCE\nKernel: $(uname -r)"
            zenity --info --title="Informações do Sistema" --text="$info"
            ;;
        2)
            thunar ~ &
            ;;
        3)
            xfce4-display-settings &
            ;;
        4)
            xdg-open "https://github.com/josenatanael9172-oss/delta-liunx-" 2>/dev/null || \
            zenity --info --title="Repositório" --text="Aceda a: https://github.com/josenatanael9172-oss/delta-liunx-"
            ;;
        *)
            break
            ;;
    esac
done
EOF
chmod +x /usr/local/bin/delta-control

cat << 'EOF' > /usr/share/applications/delta-control.desktop
[Desktop Entry]
Name=Central do Delta Linux
Comment=Painel de controlo e utilitários do Delta Linux
Exec=delta-control
Icon=preferences-desktop
Terminal=false
Type=Application
Categories=System;Settings;
EOF

# 8. Criar a aplicação de gestão de contas (Delta Accounts)
cat << 'EOF' > /usr/local/bin/delta-accounts
#!/bin/bash
if ! command -v zenity &> /dev/null; then
    echo "Erro: O zenity é necessário."
    exit 1
fi

while true; do
    acao=$(zenity --list --title="Gestor de Contas - Delta Linux" \
        --text="Gerencie os utilizadores do sistema:" \
        --column="Opção" --column="Ação Disponível" \
        1 "Criar Novo Utilizador" \
        2 "Alterar Senha" \
        3 "Listar Utilizadores" \
        4 "Sair")

    case $acao in
        1)
            user_info=$(zenity --forms --title="Criar Novo Utilizador" \
                --text="Insira os dados da nova conta:" \
                --add-entry="Nome de Utilizador" \
                --add-password="Palavra-passe")
            if [ $? -eq 0 ]; then
                novo_user=$(echo "$user_info" | awk -F '|' '{print $1}')
                nova_pass=$(echo "$user_info" | awk -F '|' '{print $2}')
                if [ -n "$novo_user" ] && [ -n "$nova_pass" ]; then
                    if id "$novo_user" &>/dev/null; then
                        zenity --error --title="Erro" --text="O utilizador já existe!"
                    else
                        useradd -m -G wheel -s /bin/bash "$novo_user"
                        echo "$novo_user:$nova_pass" | chpasswd
                        zenity --info --title="Sucesso" --text="Utilizador criado com sucesso!"
                    fi
                fi
            fi
            ;;
        2)
            pass_info=$(zenity --forms --title="Alterar Senha" \
                --text="Insira o utilizador e a nova senha:" \
                --add-entry="Nome de Utilizador" \
                --add-password="Nova Palavra-passe")
            if [ $? -eq 0 ]; then
                alvo_user=$(echo "$pass_info" | awk -F '|' '{print $1}')
                nova_senha=$(echo "$pass_info" | awk -F '|' '{print $2}')
                if [ -n "$alvo_user" ] && [ -n "$nova_senha" ]; then
                    if id "$alvo_user" &>/dev/null; then
                        echo "$alvo_user:$nova_senha" | chpasswd
                        zenity --info --title="Sucesso" --text="Senha alterada com sucesso!"
                    else
                        zenity --error --title="Erro" --text="Utilizador não encontrado!"
                    fi
                fi
            fi
            ;;
        3)
            lista_users=$(awk -F: '$3 >= 1000 && $3 != 65534 {print "- " $1}' /etc/passwd)
            zenity --info --title="Utilizadores" --text="Contas registadas:\n\n$lista_users"
            ;;
        *)
            break
            ;;
    esac
done
EOF
chmod +x /usr/local/bin/delta-accounts

cat << 'EOF' > /usr/share/applications/delta-accounts.desktop
[Desktop Entry]
Name=Contas do Delta Linux
Comment=Gerir utilizadores e senhas
Exec=delta-accounts
Icon=system-users
Terminal=false
Type=Application
Categories=Settings;System;
EOF

# 9. Criar o atalho e o comando para o Anaconda Installer / Instalação no HD
cat << 'EOF' > /usr/local/bin/anaconda-installer
#!/bin/bash
# Lançador oficial do Anaconda Installer para o Delta Linux
if command -v liveinst &> /dev/null; then
    exec liveinst
elif command -v anaconda &> /dev/null; then
    exec anaconda
else
    zenity --error --title="Erro" --text="O instalador Anaconda não foi encontrado neste ambiente."
fi
EOF
chmod +x /usr/local/bin/anaconda-installer

cat << 'EOF' > /usr/share/applications/delta-install-hd.desktop
[Desktop Entry]
Name=Instalar Delta Linux no HD
Comment=Instalar o sistema operativo permanentemente no disco rígido usando o Anaconda
Exec=anaconda-installer
Icon=drive-harddisk
Terminal=false
Type=Application
Categories=System;
StartupNotify=true
EOF

# 10. Criar o comando personalizado 'oi'
cat << 'EOF' > /usr/local/bin/oi
#!/bin/bash
clear
echo "=========================================="
echo "   OLÁ! Bem-vindo ao Delta Linux!"
echo "=========================================="
echo ""
echo "Sistema: Delta Linux"
echo "Utilizador: $USER"
echo ""
read -p "Pressione [Enter] para continuar..."
EOF
chmod +x /usr/local/bin/oi

# 11. Configurar o serviço de primeiro arranque
cat << 'EOF' > /usr/local/bin/delta-firstboot.sh
#!/bin/bash
FLAG_FILE="/var/log/delta_first_boot.done"
[ -f "$FLAG_FILE" ] && exit 0
echo "Bem-vindo ao Delta Linux!" > /etc/motd
touch "$FLAG_FILE"
systemctl disable delta-firstboot.service
EOF
chmod +x /usr/local/bin/delta-firstboot.sh

cat << 'EOF' > /etc/systemd/system/delta-firstboot.service
[Unit]
Description=Configurador do Delta Linux no Primeiro Arranque
After=graphical.target

[Service]
Type=simple
ExecStart=/usr/local/bin/delta-firstboot.sh

[Install]
WantedBy=graphical.target
EOF
systemctl enable delta-firstboot.service

# 12. Atualizar as configurações do GRUB
if [ -f /etc/default/grub ]; then
    sed -i 's/GRUB_TIMEOUT=.*/GRUB_TIMEOUT=5/' /etc/default/grub
    grub2-mkconfig -o /boot/grub2/grub.cfg
fi

echo "Instalação e personalização do Delta Linux concluídas com sucesso."
%end
Licença MIT

Copyright (c) 2026 José Natanael / Delta Linux Project

É concedida permissão, gratuitamente, a qualquer pessoa que obtenha uma cópia
deste software e dos ficheiros de documentação associados (o "Software"), para negociar
o Software sem restrições, incluindo, sem limitação, os direitos de usar, cópiar,
modificar, fundir, publicar, distribuir, sublicenciar e/ou vender cópias do Software,
e para permitir a quem o Software é fornecido o faça, sujeito às seguintes condições:

O aviso de direitos de autor acima e este aviso de permissão devem ser incluídos em todas
as cópias ou partes substanciais do Software.

O SOFTWARE É FORNECIDO "NO ESTADO EM QUE SE ENCONTRA", SEM GARANTIA DE QUALQUER TIPO,
EXPRESSA OU IMPLÍCITA, INCLUINDO MAS NÃO SE LIMITANDO Às GARANTIAS DE COMERCIALIZAÇÃO,
ADEQUAÇÃO A UM FIM ESPECÍFICO E NÃO VIOLAÇÃO. EM NENHUM CASO OS AUTORES OU TITULARES
DOS DIREITOS DE AUTOR SERÃO RESPONSÁVEIS POR QUALQUER RECLAMAÇÃO, DANOS OU OUTRA
RESPONSABILIDADE, SEJA EM AÇÃO DE CONTRATO, DELITO OU OUTRA, DECORRENTE DE, OUT OF OU
EM CONEXÃO COM O SOFTWARE OU O USO OU OUTRAS NEGOCIAÇÕES NO SOFTWARE.
