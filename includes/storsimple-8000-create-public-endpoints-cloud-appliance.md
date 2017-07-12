<a id="to-create-public-endpoints-on-the-cloud-appliance" class="xliff"></a>

#### Per creare endpoint pubblici nell'appliance cloud

1. Accedere al portale di Azure.
2. Andare a **Macchine virtuali**, quindi selezionare e fare clic sulla macchina virtuale che viene usata come appliance cloud.
    
3. È necessario creare una regola del gruppo di sicurezza di rete (NSG) per controllare il flusso di traffico in ingresso e in uscita della macchina virtuale. Per creare una regola NSG, seguire questa procedura.
    1. Selezionare **Gruppo di sicurezza di rete**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Fare clic su gruppo di sicurezza di rete predefinito che viene visualizzato.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selezionare **Regole di sicurezza in ingresso**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Fare clic su **+ Aggiungi** per creare una regola di sicurezza in ingresso.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Nel pannello Aggiungi regola di sicurezza in ingresso:

        1. In **Nome** digitare il nome seguente per l'endpoint: WinRMHttps.
        
        2. In **Priorità** selezionare un numero minore di 1000 (priorità per la regola predefinita). Maggiore è questo numero, minore è la priorità.

        3. Impostare **Origine** su **Qualsiasi**.

        4. In **Servizio** selezionare **WinRM**. **Protocollo** viene automaticamente impostato su **TCP** e **Intervallo di porte** viene impostato su **5986**.

        5. Fare clic su **OK** per creare la regola.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete a una subnet o un'interfaccia di rete specifica. Seguire questa procedura per associare il gruppo di sicurezza di rete a una subnet.
    1. Andare a **Subnet**.
    2. Fare clic su **+ Associa**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selezionare la rete virtuale, quindi selezionare la subnet appropriata.
    4. Fare clic su **OK** per creare la regola.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Dopo avere creato la regola, è possibile visualizzarne i dettagli e determinare l'indirizzo IP virtuale pubblico (indirizzo VIP). Registrare tale indirizzo.


