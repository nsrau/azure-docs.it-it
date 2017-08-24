

![Macchine virtuali in un servizio cloud autonomo](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Se si inseriscono le macchine virtuali in una rete virtuale, è possibile decidere quanti servizi cloud usare per il bilanciamento del carico e i set di disponibilità. Inoltre, è possibile organizzare le macchine virtuali nelle subnet analogamente alla rete locale e connettere la rete virtuale alla rete locale. Ad esempio:

![Macchine virtuali in una rete virtuale](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Le reti virtuali sono il metodo consigliato per connettere le macchine virtuali in Azure. La procedura consigliata consiste nel configurare ogni livello dell'applicazione in un servizio cloud separato. Tuttavia, potrebbe essere necessario combinare le macchine virtuali da diversi livelli dell'applicazione nello stesso servizio cloud in modo da rimanere entro il numero massimo di 200 servizi cloud per ogni sottoscrizione. Per esaminare questo e altri limiti, vedere [sottoscrizione Azure e limiti, quote e vincoli del servizio](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Connettere le macchine virtuali in una rete virtuale
Per connettere le macchine virtuali in una rete virtuale:

1. Creare la rete virtuale nel [portale di Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) e specificare "distribuzione classica".
2. Creare il set di servizi cloud per la tua distribuzione al fine di mostrare il tuo design per i set di disponibilità e il bilanciamento del carico. Nel portale di Azure fare clic su **Nuovo > Calcolo > Servizio cloud** per ogni servizio cloud.

  Durante la compilazione dei dettagli del servizio cloud, scegliere lo stesso _gruppo di risorse_ usato con la rete virtuale.

3. Per creare ogni nuova macchina virtuale, fare clic su **Nuovo > Calcolo** e selezionare l'immagine di macchina virtuale appropriata da **App in primo piano**.

  Nel pannello **Nozioni di base** della macchina virtuale scegliere lo stesso _gruppo di risorse_ usato con la rete virtuale.

  ![Pannello Nozioni di base della macchina virtuale quando si usa una rete virtuale](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Durante la compilazione delle **impostazioni** della macchina virtuale, scegliere il _servizio cloud_ o la _rete virtuale_ corretta per la macchina virtuale.

  Azure selezionerà l'altro elemento in base a quello selezionato.

  ![Pannello Impostazioni della macchina virtuale quando si usa una rete virtuale](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Connettere le macchine virtuali in un servizio cloud autonomo
Per connettere le macchine virtuali in un servizio cloud autonomo:

1. Creare il servizio cloud nel [portale di Azure](http://portal.azure.com). Fare clic su **Nuovo > Calcolo > Servizio cloud**. In alternativa, è possibile creare il servizio cloud per la distribuzione quando si crea la prima macchina virtuale.
2. Durante la creazione delle macchine virtuali, scegliere lo stesso gruppo di risorse usato con il servizio cloud.

  ![Aggiungere una macchina virtuale a un servizio cloud esistente](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Durante la compilazione dei dettagli della macchina virtuale, scegliere il nome del servizio cloud creato nel primo passaggio.

  ![Selezione di un servizio cloud per una macchina virtuale](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
