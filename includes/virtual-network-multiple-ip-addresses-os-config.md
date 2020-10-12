---
title: includere file
description: includere file
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 93caf39216ef0479ec2799267a9ba8181f37f802
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84194208"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Aggiungere indirizzi IP a un sistema operativo VM

Connettersi e accedere alla VM creata con più indirizzi IP privati. È necessario aggiungere manualmente tutti gli indirizzi IP privati aggiunti alla VM, incluso l'indirizzo primario. Completare i passaggi seguenti per il sistema operativo della macchina virtuale.

### <a name="windows-server"></a>Windows Server
<details>
  <summary>Espandere</summary>

1. Da un prompt dei comandi digitare *ipconfig /all*.  Viene visualizzato solo l'indirizzo IP privato *Primary* , tramite DHCP.
2. Digitare *ncpa.cpl* nel prompt dei comandi per aprire la finestra **Connessioni di rete**.
3. Visualizzare le proprietà per la scheda appropriata: **Ethernet**.
4. Fare doppio clic su Protocollo Intenret versione 4 (IPv4).
5. Selezionare **Utilizza il seguente indirizzo IP** e immettere i valori seguenti:

    * **Indirizzo IP**: immettere l'indirizzo IP privato *Primario*
    * **Subnet mask**: configurare questo valore in base alla subnet. Se, ad esempio, la subnet è di tipo /24, la subnet mask è 255.255.255.0.
    * **Gateway predefinito**: primo indirizzo IP nella subnet. Se la subnet è 10.0.0.0/24, l'indirizzo IP del gateway è 10.0.0.1.
    * Selezionare **Utilizza i seguenti indirizzi server DNS** e immettere i valori seguenti:
        * **Server DNS preferito**: immettere 168.63.129.16 se non si usa il proprio server DNS.  Se si usa il proprio server DNS, immettere il relativo indirizzo IP.  Per un server DNS alternativo è possibile selezionare qualsiasi indirizzo del server DNS pubblico gratuito.
    * Selezionare il pulsante **Avanzate** e aggiungere altri indirizzi IP. Aggiungere tutti gli indirizzi IP secondari, aggiunti all'interfaccia di rete di Azure in un passaggio precedente, all'interfaccia di rete di Windows assegnata all'indirizzo IP primario assegnato all'interfaccia di rete di Azure.

        Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale. Quando si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](../articles/virtual-network/virtual-network-network-interface-addresses.md#private). L'assegnazione di un indirizzo IP pubblico di Azure all'interno del sistema operativo è un'operazione sempre sconsigliata.

    * Fare clic su **OK** per chiudere le impostazioni TCP/IP e quindi di nuovo su **OK** per chiudere le impostazioni della scheda. Viene ristabilita la connessione RDP.

6. Da un prompt dei comandi digitare *ipconfig /all*. Verificare che tutti gli indirizzi IP aggiunti siano visualizzati e che DHCP sia disattivato.
7. Configurare Windows per usare l'indirizzo IP privato della configurazione IP primaria in Azure come indirizzo IP primario per Windows. Per i dettagli, vedere [Nessun accesso a Internet da una macchina virtuale Windows Azure che dispone di più indirizzi IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse). 

### <a name="validation-windows-server"></a>Convalida (Windows Server)

Per assicurarsi che sia possibile connettersi a Internet dalla configurazione dell'indirizzo IP secondaria tramite l'indirizzo IP ad essa associato, usare il comando seguente dopo averlo aggiunto correttamente seguendo la procedura precedente (sostituendo 10.0.0.7 con l'indirizzo IP privato secondario):

```bash
ping -S 10.0.0.7 outlook.com
```
>[!NOTE]
>Per le configurazioni IP secondarie, è possibile effettuare il ping a Internet solo se alla configurazione è associato un indirizzo IP pubblico. Per le configurazioni IP primarie, non è necessario un indirizzo IP pubblico per il ping a Internet.
</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
<details>
  <summary>Espandere</summary>
Si consiglia di esaminare la documentazione più recente per la distribuzione di Linux. 

1. Aprire una finestra del terminale.
2. Assicurarsi di essere l'utente ROOT. In caso contrario, immettere il comando seguente:

   ```bash
   sudo -i
   ```

3. Aggiornare il file di configurazione dell'interfaccia di rete, presupponendo 'eth0'.

   * Mantenere la voce esistente per dhcp. L'indirizzo IP primario conserva la configurazione precedente.
   * Aggiungere una configurazione per un indirizzo IP statico aggiuntivo con i comandi seguenti:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Dovrebbe essere visualizzato un file con estensione cfg.
4. Open the file. Dovrebbero essere visualizzate le righe seguenti alla fine del file:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Aggiungere le righe seguenti dopo le righe esistenti nel file:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Salvare il file usando il comando seguente:

   ```bash
   :wq
   ```

7. Reimpostare l'interfaccia di rete con il comando seguente:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Eseguire ifdown e ifup nella stessa riga se si usa una connessione remota.
   >

8. Verificare che l'indirizzo IP venga aggiunto all'interfaccia di rete con il comando seguente:

   ```bash
   ip addr list eth0
   ```

   L'indirizzo IP aggiunto dovrebbe essere incluso nell'elenco.

### <a name="validation-ubuntu-1416"></a>Convalida (Ubuntu 14/16)

Per assicurarsi che sia possibile connettersi a Internet dalla configurazione dell'indirizzo IP secondaria tramite l'indirizzo IP ad essa associato, usare il comando seguente:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Per le configurazioni IP secondarie, è possibile effettuare il ping a Internet solo se alla configurazione è associato un indirizzo IP pubblico. Per le configurazioni IP primarie, non è necessario un indirizzo IP pubblico per il ping a Internet.

Per le macchine virtuali Linux, quando si prova a convalidare la connettività in uscita da una scheda di interfaccia di rete secondaria, potrebbe essere necessario aggiungere le route appropriate. Per eseguire questa operazione è possibile procedere in molti modi. Per informazioni sulla distribuzione Linux, vedere la documentazione appropriata. Ecco un metodo per ottenere questo risultato:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Assicurarsi di sostituire:
    - **10.0.0.5** con l'indirizzo IP privato a cui è associato un indirizzo IP pubblico
    - **10.0.0.1** con il gateway predefinito
    - **eth2** con il nome della scheda di interfaccia di rete secondaria </details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)
<details>
  <summary>Espandere</summary>
Ubuntu 18.04 e versioni successive sono stati modificati in `netplan` per la gestione della rete del sistema operativo. Si consiglia di esaminare la documentazione più recente per la distribuzione di Linux. 

1. Aprire una finestra del terminale.
2. Assicurarsi di essere l'utente ROOT. In caso contrario, immettere il comando seguente:

    ```bash
    sudo -i
    ```

3. Creare un file per la seconda interfaccia e aprirlo in un editor di testo:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Aggiungere le righe seguenti al file, sostituendo `10.0.0.6/24` con l'IP/netmask usati:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Salvare il file usando il comando seguente:

    ```bash
    :wq
    ```

6. Testare le modifiche usando [netplan try](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) per verificare la sintassi:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try` applicherà le modifiche temporaneamente ed eseguirà il rollback delle modifiche dopo 120 secondi. Se si verifica una perdita di connettività, attendere 120 secondi e quindi riconnettersi. A quel punto, sarà stato eseguito il rollback delle modifiche.

7. Se non si verificano problemi con `netplan try`, applicare le modifiche alla configurazione:

    ```bash
    netplan apply
    ```

8. Verificare che l'indirizzo IP venga aggiunto all'interfaccia di rete con il comando seguente:

    ```bash
    ip addr list eth0
    ```

    L'indirizzo IP aggiunto dovrebbe essere incluso nell'elenco. Esempio:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
### <a name="validation-ubuntu-1804"></a>Convalida (Ubuntu 18.04+)

Per assicurarsi che sia possibile connettersi a Internet dalla configurazione dell'indirizzo IP secondaria tramite l'indirizzo IP ad essa associato, usare il comando seguente:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Per le configurazioni IP secondarie, è possibile effettuare il ping a Internet solo se alla configurazione è associato un indirizzo IP pubblico. Per le configurazioni IP primarie, non è necessario un indirizzo IP pubblico per il ping a Internet.

Per le macchine virtuali Linux, quando si prova a convalidare la connettività in uscita da una scheda di interfaccia di rete secondaria, potrebbe essere necessario aggiungere le route appropriate. Per eseguire questa operazione è possibile procedere in molti modi. Per informazioni sulla distribuzione Linux, vedere la documentazione appropriata. Ecco un metodo per ottenere questo risultato:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Assicurarsi di sostituire:
    - **10.0.0.5** con l'indirizzo IP privato a cui è associato un indirizzo IP pubblico
    - **10.0.0.1** con il gateway predefinito
    - **eth2** con il nome della scheda di interfaccia di rete secondaria </details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (RedHat, CentOS e altri)
<details>
  <summary>Espandere</summary>

1. Aprire una finestra del terminale.
2. Assicurarsi di essere l'utente ROOT. In caso contrario, immettere il comando seguente:

    ```bash
    sudo -i
    ```

3. Immettere la password e seguire le istruzioni visualizzate. Quando si è l'utente ROOT, passare alla cartella degli script di rete con il comando seguente:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Elencare i file ifcfg correlati usando il comando seguente:

    ```bash
    ls ifcfg-*
    ```

    Uno dei file visualizzati dovrebbe essere *ifcfg-eth0* .

5. Per aggiungere un indirizzo IP, creare un file di configurazione come illustrato di seguito. Si noti che è necessario creare un file per ogni configurazione IP.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Aprire il file *ifcfg-eth0:0* con il comando seguente:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Aggiungere contenuto al file, in questo caso *eth0:0*, con il comando seguente. Assicurarsi di aggiornare le informazioni in base all'indirizzo IP.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Salvare il file usando il comando seguente:

    ```bash
    :wq
    ```

9. Riavviare i servizi di rete e assicurarsi che le modifiche siano riuscite eseguendo i comandi seguenti:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    L'indirizzo IP aggiunto, *eth0:0*, dovrebbe essere incluso nell'elenco restituito.

### <a name="validation-red-hat-centos-and-others"></a>Convalida (RedHat, CentOS e altri)

Per assicurarsi che sia possibile connettersi a Internet dalla configurazione dell'indirizzo IP secondaria tramite l'indirizzo IP ad essa associato, usare il comando seguente:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Per le configurazioni IP secondarie, è possibile effettuare il ping a Internet solo se alla configurazione è associato un indirizzo IP pubblico. Per le configurazioni IP primarie, non è necessario un indirizzo IP pubblico per il ping a Internet.

Per le macchine virtuali Linux, quando si prova a convalidare la connettività in uscita da una scheda di interfaccia di rete secondaria, potrebbe essere necessario aggiungere le route appropriate. Per eseguire questa operazione è possibile procedere in molti modi. Per informazioni sulla distribuzione Linux, vedere la documentazione appropriata. Ecco un metodo per ottenere questo risultato:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Assicurarsi di sostituire:
    - **10.0.0.5** con l'indirizzo IP privato a cui è associato un indirizzo IP pubblico
    - **10.0.0.1** con il gateway predefinito
    - **eth2** con il nome della scheda di interfaccia di rete secondaria </details>