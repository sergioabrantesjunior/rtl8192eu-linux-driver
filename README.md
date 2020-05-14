# rtl8192eu

# Problema
Driver de rede do TP-Link TL WN823N RTL8192EU USB

Utilizando Debian Testing, atual bullseye ( e no stable também buster ), esse adaptador não funciona, mesmo instalando o firmware-realtek e outros pacotes non-free disponíveis.

No indicador do network-manager do Cinnamon mostra as redes disponíveis, mas não conecta. Esse é o problema.

Com o comando lsusb podemos ver que o driver rtl8xxxu está associado a placa (Driver=rtl8xxxu):

```
# lsusb -tv
/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=xhci_hcd/10p, 480M
    ID 1d6b:0002 Linux Foundation 2.0 root hub
    |__ Port 1: Dev 2, If 0, Class=Vendor Specific Class, Driver=rtl8xxxu, 480M
        ID 2357:0109 TP-Link TL WN823N RTL8192EU
```

# Solução

Vamos compilar o driver fornecido pelo fabricante utilizando o dkms (apt install dkms) para garantir que a cada nova versão do kernel (no testing sempre está mudando) ele seja recompilado automaticamente para o novo kernel.

1. Baixar o fonte e descompactá-lo
```
cd /opt
git clone https://github.com/sergioabrantesjunior/rtl8192eu-linux-driver.git
```
2. Compilar

```
dkms add rtl8192eu-linux-driver
dkms build -m rtl8192eu/1.0
dkms install rtl8192eu/1.0
```
3. Bloquear o carregamento do módulo errado
```
echo "blacklist rtl8xxxu" >/etc/modprobe.d/blacklist-rtl8xxxu.conf
```

# Conferindo e testando

No meu caso consegui remover o driver antigo com o comando "modprobe -r rtl8xxxu", mas para subir o novo com o comando "modprobe rtl8192eu" deu alguns problemas. **Então reboot**.

Agora podemos fazer o mesmo comando feito no início para conferir o driver:
```
# lsusb -tv
/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=xhci_hcd/10p, 480M
    ID 1d6b:0002 Linux Foundation 2.0 root hub
    |__ Port 1: Dev 2, If 0, Class=Vendor Specific Class, Driver=rtl8192eu, 480M
        ID 2357:0109 TP-Link TL WN823N RTL8192EU
```

Agora você conseguirá conectar na rua rede normalmente



# Referências
[Base para os comandos](https://salsa.debian.org/kretcheu/tutoriais/-/blob/master/dkms.md)

[Fonte do driver](https://github.com/Mange/rtl8192eu-linux-driver)


