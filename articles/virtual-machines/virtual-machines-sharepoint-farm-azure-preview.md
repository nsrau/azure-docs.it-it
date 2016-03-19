<properties
	pageTitle="Creare farm di SharePoint Server | Microsoft Azure"
	description="È possibile creare rapidamente una nuova farm base o a disponibilità elevata di SharePoint Server 2013 con il marketplace nel portale di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="josephd"/>

# Creare farm di SharePoint Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello classico.

Con il marketplace del portale di Microsoft Azure, è possibile creare rapidamente delle farm pre-configurate di SharePoint Server 2013. Ciò consente di risparmiare una notevole quantità di tempo in caso si necessiti di una farm di SharePoint di base o a disponibilità elevata per un ambiente di sviluppo e test o in caso si stia valutando l'opportunità di usare SharePoint Server 2013 come soluzione per la collaborazione all'interno dell'organizzazione.

> [AZURE.NOTE] L'elemento **SharePoint Farm** nella sezione Marketplace del portale di Azure è stato rimosso. È stato sostituito dagli elementi **SharePoint 2013 non-HA Farm** e **SharePoint 2013 HA Farm**.

La farm di SharePoint di base è costituita da tre macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

È possibile usare questa configurazione di farm per un'installazione semplificata per lo sviluppo di app SharePoint o per la prima valutazione di SharePoint 2013.

Per creare la farm di SharePoint (tre server) di base:

1. Fare clic [qui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)
2. Fare clic su **Distribuisci**.
3. Nel riquadro **SharePoint 2013 non-HA Farm**, fare clic su **Crea**.
4. Specificare le impostazioni nei 7 passaggi del riquadro **Creare una SharePoint 2013 non-HA Farm**, quindi fare clic su **Crea**.

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

È possibile usare questa configurazione di farm per testare carichi client più elevati, la disponibilità elevata del sito di SharePoint esterno e i gruppi di disponibilità AlwaysOn per una farm di SharePoint. È anche possibile usare questa configurazione per lo sviluppo di app SharePoint in un ambiente a disponibilità elevata.

Per creare la farm di SharePoint a disponibilità elevata (nove server):

1. Fare clic [qui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)
2. Fare clic su **Distribuisci**.
3. Nel riquadro **SharePoint 2013 HA Farm**, fare clic su **Crea**.
4. Specificare le impostazioni per i 7 passaggi del riquadro **Creare una SharePoint 2013 HA Farm**, quindi fare clic su **Crea**.

## Gestire le farm di SharePoint

È possibile amministrare i server di queste farm tramite connessioni Desktop Remoto. Per ulteriori informazioni, vedere [Accedere alla macchina virtuale](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine).

Dal sito Amministrazione centrale di SharePoint è possibile configurare i siti personali, le applicazioni di SharePoint e altre funzionalità. Per altre informazioni, vedere [Configurare SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

> [AZURE.NOTE] Il portale di Azure crea entrambe queste farm in una rete virtuale solo cloud con una presenza Web con connessione Internet. Non esiste alcuna connessione VPN o ExpressRoute da sito a sito alla rete dell'organizzazione.

## Passaggio successivo

- Scoprire altre configurazioni [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) nei servizi di infrastruttura di Azure.

<!---HONumber=AcomDC_0211_2016-->