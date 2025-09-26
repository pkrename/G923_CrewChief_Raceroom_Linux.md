
# 🎮 Tutorial: Logitech G923 + RaceRoom + CrewChief no Linux (Nobara + Steam + SteamTinkerLaunch)

📅 **Data:** 18/06/2025  
👤 **Autor:** Pedro Santana  

---
> ⚠️ **This tutorial is in Portuguese-BR first. Scroll down for the English version.**
> ⚠️ **Este tutorial está em Português-BR. A versão em inglês está disponível após o conteúdo em português.**

---

## ✅ Introdução

Olá pessoal!  
Este tutorial serve para ajudar quem está tentando rodar o volante **Logitech G923 Xbox Edition**, o jogo **RaceRoom** e o aplicativo **CrewChief** no **Linux**, utilizando **Nobara Linux**, **Steam**, **Proton** e **SteamTinkerLaunch (STL)**.

### 🖥️ **Setup Utilizado:**

- **Processador:** Ryzen 5 5600X  
- **Placa-mãe:** ASUS Prime B550M-A WiFi II  
- **Memória:** 32GB Kingston 3200MHz  
- **Placa de vídeo:** ASUS Dual RTX 3060 12GB OC Edition  
- **Sistema operacional:** Nobara Linux 42  
- **Kernel:** 6.15.2-200.nobara.fc42.x86_64  
- **Proton:** Proton Hotfix 20250501  

---

## 🎯 Problema

- O volante **Logitech G923 Xbox Edition** não era reconhecido corretamente.
- O **CrewChief** não conseguia se conectar ao **RaceRoom**, pois precisa rodar na mesma máquina virtual (prefixo Proton) para acessar dados de telemetria.

---

## 🔧 Solução para o Volante

Execute o comando abaixo para que o volante seja reconhecido corretamente:
```bash
sudo usb_modeswitch -v 046d -p c26d -M 0f00010142 -C 0x03 -m 01 -r 01
```

Após isso, o volante reiniciará, fará o auto-teste e será reconhecido no menu `Controles de Jogo` do sistema.

Instale o **Oversteer** via Flatpak para configuração de Force Feedback:
```bash
flatpak install flathub io.github.berarma.Oversteer
```

Conforme **Berarma**, para poder produzir os ajustes do volante, precisamos organizar as permissões:
https://github.com/berarma/oversteer/blob/master/data/udev/99-logitech-wheel-perms.rules
```bash
sudo cp ~/Downloads/99-logitech-wheel-perms.rules /lib/udev/rules.d/
sudo udevadm control --reload
sudo udevadm trigger
```

Com isso seu volante já estará com permissões para ajustes.
---


## 🚥 Solução para o CrewChief + RaceRoom

### 🗂️ Passo 1 - Preparar Prefixo do CrewChief

Instalando pacotes necessários:
```bash
sudo dnf install yad xdotool xwininfo protontricks
```

Crie um prefixo separado:
```bash
WINEPREFIX=~/crewchief_wine_prefix winetricks -q dotnet48
```

Ignore pop-ups relacionados ao mono se eles aparecerem

Instale o CrewChief:
```bash
WINEPREFIX=~/crewchief_wine_prefix wine ~/Downloads/CrewChiefV4.msi
```

⚠️ Durante a instalação, marque a opção **“Launch CrewChief”** no final.

### 🔊 Atualizar Pacotes de Áudio

- Não clique ainda em "Start Crew Chief".  
- Clique nos 3 botões **“Sound Pack Status, Driver Names, Personalisations"** e deixe ele atualizar todos.  
- Serão várias reinicializações do CrewChief (mais de 20 às vezes).
Então feche o CrewChief



### 🚀 Rodar o CrewChief no Prefixo do Jogo

Rode o seguinte comando:
```bash
protontricks-launch ~/crewchief_wine_prefix/drive_c/Program\ Files\ \(x86\)/Britton\ IT\ Ltd/CrewChiefV4/CrewChiefV4.exe
```

Escolha o jogo e clique em OK

### 🔧 Ajustar Configurações

No menu **Properties**:

- Desmarque **“Use nAudio for playback”**.  
- Marque **“Enable WebHud Integration (R3E only)”**.  
- Marque **“Run Immediately”**.

Descubra o AppID do seu jogo:
```bash
grep name ~/.steam/steam/steamapps/appmanifest_*
```

No caso do **RaceRoom**, geralmente é **211500**.

Copie os arquivos de áudio para o prefixo do jogo:
```bash
cp -r ~/crewchief_wine_prefix/drive_c/users/$USER/AppData/Local/CrewChiefV4/ ~/.steam/steam/steamapps/compatdata/211500/pfx/drive_c/users/steamuser/AppData/Local/
cp -r ~/crewchief_wine_prefix/drive_c/users/$USER/AppData/Local/Britton_IT_Ltd ~/.steam/steam/steamapps/compatdata/211500/pfx/drive_c/users/steamuser/AppData/Local/
```

---

## 🚦 Rodar CrewChief + RaceRoom Simultaneamente

### ✅ Instalar o **SteamTinkerLaunch (STL)**

Se estiver no **Nobara**, basta instalar pelo **ProtonPlus**, reiniciar a Steam e selecionar o STL como camada de compatibilidade do RaceRoom.

### ⚙️ Configurar o STL

1. Execute o RaceRoom (ele abrirá o menu do STL).  
2. Clique em **Main Menu → Game Menu**.  
3. Marque **“Use Custom Command”**.  
4. Em **Custom Command**, coloque o caminho do CrewChief:
```
/home/seuusuario/crewchief_wine_prefix/drive_c/Program Files (x86)/Britton IT Ltd/CrewChiefV4/CrewChiefV4.exe
```

5. Marque **“Fork Custom Command”**.
6. Marque **"Pass external programs to custom command"**
7. Marque **"Use Steam Linux Runtime with Custom Command"**
8. Marque **"Force Proton with custom command"**
9. Em Wait **"Custom Command"** coloque 40 segundos (você pode baixar o tempo depois, é só para a primeira inicialização)
10. Em **Game Command Arguments (custom)** coloque (para ativar o HUD):  
```
-webHudUrl=https://sealhud.github.io/
```

11. Selecione a versão do Proton desejada (Proton Hotfix recomendado, o que usei é a versão 20250501).
12. Marque **"Use Steam Linux Runtime"**
13. Marque **"Force Steam Linux Runtime"**
14. Clique em **Save → Back → Exit**.
15. Antes de rodar o jogo, instale o dotnet48 no prefixo do RaceRoom: (use esse comando abaixo, pois sei que o dotnet48 será instalado corretamente)
```bash
STEAM_COMPAT_DATA_PATH=~/.steam/steam/steamapps/compatdata/211500 \
WINEPREFIX=~/.steam/steam/steamapps/compatdata/211500/pfx \
winetricks -q dotnet48
```

**Na primeira execução irá produzir um erro do rundll32.exe, pode clicar em NÃO**

Rode o jogo e verifique se as configurações do CrewChief estão corretas (geralmente o game fica errado, verifique também se seu nome está configurado corretamente), se não, aumente o tempo do Wait Custom Command.

Se algo der errado, verifique novamente as configurações descritas acima.

Execute novamente o jogo e estará funcionando com CrewChief e WebHud.

### 🔄 Resetar Configurações do STL (se necessário)

Se configurar algo errado no STL:
```bash
cd ~/.config/steamtinkerlaunch/gamecfgs/
mv 211500.conf 211500.conf_bkp
```

O arquivo será regenerado na próxima execução.

---

## 🚀 Conclusão

Após esse processo, meu setup está 100% funcional no Linux. CrewChief operando normalmente, volante totalmente funcional com Force Feedback, e HUD integrado via SealHud (port do OtterHud).

---

English Version:

# 🇬🇧 Tutorial: Logitech G923 + RaceRoom + CrewChief on Linux (Nobara + Steam + SteamTinkerLaunch)

📅 **Date:** 2025-06-18  
👤 **Author:** Pedro Santana  

---

## ✅ Introduction

This guide is intended for users running the **Logitech G923 Xbox Edition**, **RaceRoom**, and **CrewChief** on **Linux**, using **Nobara Linux**, **Steam**, **Proton**, and **SteamTinkerLaunch (STL)**.

### 🖥️ **My Setup:**

- **CPU:** Ryzen 5 5600X  
- **Motherboard:** ASUS Prime B550M-A WiFi II  
- **Memory:** 32GB Kingston 3200MHz  
- **GPU:** ASUS Dual RTX 3060 12GB OC Edition  
- **OS:** Nobara Linux 42  
- **Kernel:** 6.15.2-200.nobara.fc42.x86_64  
- **Proton:** Proton Hotfix 20250501  

---

## 🎯 Problem

- The **Logitech G923 Xbox Edition** wheel was not recognized correctly.  
- **CrewChief** could not connect to **RaceRoom** because both must run in the same Proton prefix to access telemetry data.

---

## 🔧 Solution for the Wheel

Run:
```bash
sudo usb_modeswitch -v 046d -p c26d -M 0f00010142 -C 0x03 -m 01 -r 01
```

This resets the wheel into the correct mode. Then install **Oversteer** via Flatpak:
```bash
flatpak install flathub io.github.berarma.Oversteer
```

According to **Berarma**, in order to make adjustments to the steering wheel, we need to organize the permissions:
https://github.com/berarma/oversteer/blob/master/data/udev/99-logitech-wheel-perms.rules
```bash
sudo cp ~/Downloads/99-logitech-wheel-perms.rules /lib/udev/rules.d/
sudo udevadm control --reload
sudo udevadm trigger
```

With this, your steering wheel will already have permissions for adjustments.

---

## 🚥 Solution for CrewChief + RaceRoom

Installing necessary packages:
```bash
sudodnf install yad xdotool xwininfo protontricks
```

### 🗂️ Step 1 - Prepare CrewChief Prefix

Ignore mono related popups if they appear
```bash
WINEPREFIX=~/crewchief_wine_prefix winetricks -q dotnet48
WINEPREFIX=~/crewchief_wine_prefix wine ~/Downloads/CrewChiefV4.msi
```

Mark **“Launch CrewChief”** after installation.

### 🔊 Update Sound Packs

- Do NOT click “Start Crew Chief” yet.  
- Click the 3 **“Sound Pack Status, Driver Names, Personalisations"** buttons until all are updated.  
- Expect ~20 restarts.
Then close CrewChief.

### 🚀 Run CrewChief in Game Prefix

Run the following command:
```bash
protontricks-launch ~/crewchief_wine_prefix/drive_c/Program\ Files\ \(x86\)/Britton\ IT\ Ltd/CrewChiefV4/CrewChiefV4.exe
```

Choose the game and click OK.

### 🔧 Configuration

In **Properties**:

- Uncheck **“Use nAudio for playback”**.  
- Check **“Enable WebHud Integration (R3E only)”**.  
- Check **“Run Immediately”**.

Find AppID:
```bash
grep name ~/.steam/steam/steamapps/appmanifest_*
```

**RaceRoom is AppID 211500.**

Copy sound files:
```bash
cp -r ~/crewchief_wine_prefix/drive_c/users/$USER/AppData/Local/CrewChiefV4/ ~/.steam/steam/steamapps/compatdata/211500/pfx/drive_c/users/steamuser/AppData/Local/
cp -r ~/crewchief_wine_prefix/drive_c/users/$USER/AppData/Local/Britton_IT_Ltd ~/.steam/steam/steamapps/compatdata/211500/pfx/drive_c/users/steamuser/AppData/Local/
```

---

## 🚦 Run CrewChief + RaceRoom Together

### ✅ Install **SteamTinkerLaunch (STL)**

On **Nobara**, install via **ProtonPlus**, restart Steam, and select STL as compatibility for RaceRoom.

### ⚙️ Configure STL

1. Run RaceRoom (STL menu will open).  
2. Go to **Main Menu → Game Menu**.  
3. Enable **“Use Custom Command”**.  
4. Set **Custom Command** to:
```
/home/youruser/crewchief_wine_prefix/drive_c/Program Files (x86)/Britton IT Ltd/CrewChiefV4/CrewChiefV4.exe
```

5. Enable **“Fork Custom Command”**.
6. Enable **"Pass external programs to custom command"**
7. Enable **"Use Steam Linux Runtime with Custom Command"**
8. Enable **"Force Proton with custom command"**
9. In **"Wait Custom Command"**, set it to 40 seconds (you can lower the time later, it's only for the first boot)
10. In **Game Command Arguments (custom)**, add:
```
-webHudUrl=https://sealhud.github.io/
```

11. Set your preferred Proton version (Proton Hotfix recommended, the one I used is version 20250501).
12. Enable **"Use Steam Linux Runtime"**
13. Enable **"Force Steam Linux Runtime"**
14. **Save → Back → Exit.**
15. Before running the game, install dotnet48 in the RaceRoom prefix: (use this command below, as I know that dotnet48 will be installed correctly)
```bash
STEAM_COMPAT_DATA_PATH=~/.steam/steam/steamapps/compatdata/211500 \
WINEPREFIX=~/.steam/steam/steamapps/compatdata/211500/pfx \
winetricks -q dotnet48
```

**The first time you run it, it will produce a rundll32.exe error, you can click NO**

Run the game and check if the CrewChief settings are correct (usually the game gets it wrong, also check if your name is configured correctly), if not, increase the Wait Custom Command time.

If something goes wrong, double-check the settings described above.

### 🔄 Reset STL Config (if needed)
```bash
cd ~/.config/steamtinkerlaunch/gamecfgs/
mv 211500.conf 211500.conf_bkp
```

---

## 🚀 Conclusion

Everything works flawlessly now: the Logitech G923 with proper Force Feedback, CrewChief with telemetry, and the WebHUD.

---

## ☕ Contribute

If this guide helped you, feel free to contribute, improve this document, or share your feedback!

---
