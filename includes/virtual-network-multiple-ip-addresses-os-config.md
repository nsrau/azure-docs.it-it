## <a name="a-nameos-configaadd-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Add IP addresses to a VM operating system (Aggiungere indirizzi IP a un sistema operativo VM)

Connettersi e accedere alla VM creata con più indirizzi IP privati. È necessario aggiungere manualmente tutti gli indirizzi IP privati aggiunti alla VM, incluso l'indirizzo primario. Completare i passaggi seguenti per il sistema operativo VM:

### <a name="windows"></a>Windows

1. Da un prompt dei comandi digitare *ipconfig /all*.  Viene visualizzato solo l'indirizzo IP privato *Primary* , tramite DHCP.
2. Digitare *ncpa.cpl* nel prompt dei comandi per aprire la finestra **Connessioni di rete**.
3. Aprire le proprietà per**Connessione alla rete locale (LAN)**.
4. Fare doppio clic su Protocollo Intenret versione 4 (IPv4).
5. Selezionare **Utilizza il seguente indirizzo IP** e immettere i valori seguenti:

    * **Indirizzo IP**: immettere l'indirizzo IP privato *Primary* .
    * **Subnet mask**: configurare questo valore in base alla subnet. Se, ad esempio, la subnet è di tipo /24, la subnet mask è 255.255.255.0.
    * **Gateway predefinito**: primo indirizzo IP nella subnet. Se la subnet è 10.0.0.0/24, l'indirizzo IP del gateway è 10.0.0.1.
    * Fare clic su **Utilizza i seguenti indirizzi server DNS** e immettere i valori seguenti:
        * **Server DNS preferito**: immettere 168.63.129.16 se non si usa il proprio server DNS.  Se si usa il proprio server DNS, immettere il relativo indirizzo IP.
    * Fare clic sul pulsante **Avanzate** e aggiungere altri indirizzi IP. Aggiungere ogni indirizzo IP privato secondario elencato nel passaggio 8 all'interfaccia di rete con la stessa subnet specificata per l'indirizzo IP primario.
    * Fare clic su **OK** per chiudere le impostazioni TCP/IP e quindi di nuovo su **OK** per chiudere le impostazioni della scheda. Viene ristabilita la connessione RDP.
6. Da un prompt dei comandi digitare *ipconfig /all*. Tutti gli indirizzi IP aggiunti vengono visualizzati e DHCP viene disattivato.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

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
4. Aprire il file:

        ```bash
        vi eth0.cfg
        ```

    Dovrebbero essere visualizzate le righe seguenti alla fine del file:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Aggiungere le righe seguenti dopo le righe esistenti nel file:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
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
    Ip addr list eth0
    ```

    L'indirizzo IP aggiunto dovrebbe essere incluso nell'elenco.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS e altro)

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


<!--HONumber=Feb17_HO1-->


