<properties 
   pageTitle="Come impostare un indirizzo IP statico privato in modalità classica usando il portale di Azure | Microsoft Azure"
   description="Informazioni sugli IP statici privati e istruzioni per gestirli in modalità classica tramite il portale di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Come impostare un indirizzo IP statico privato (classico) nel portale di Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo articolo viene illustrato il modello di distribuzione classica. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione di gestione delle risorse](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente prevede che un ambiente semplice sia già creato. Se si desidera eseguire la procedura illustrata in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-classic-pportal.md).

## Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale.
Per creare una macchina virtuale denominata *DNS01* nel subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **NEW** > **Calcolo** > **Windows Server 2012 R2 Datacenter**, si noti che l’elenco **Selezionare un modello di distribuzione** mostra già **Classico**, quindi fare clic su **Crea**.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Nel pannello **Creare VM**, immettere il nome della macchina virtuale da creare (*DNS01* nello scenario), l'account amministratore locale e la password.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Fare clic su **Configurazione facoltativa** > **Rete** > **Rete virtuale**, poi fare clic su **TestVNet**. Se **TestVNet** non è disponibile, verificare che si utilizzi la posizione *Stati Uniti centrali* e di aver creato l'ambiente di prova descritto all'inizio di questo articolo.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. Nel pannello **Rete**, assicurarsi che la subnet attualmente selezionata sia *FrontEnd*, poi fare clic su **indirizzi IP**, in **assegnazione di indirizzi IP** fare clic su **Statico**, e poi immettere *192.168.1.101* per **Indirizzo IP** come indicato di seguito.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)

6. Fare clic su **OK** nel pannello**indirizzi IP**, poi fare clic su **OK** nel pannello **Rete**, e fare clic su **OK** nel pannello **Configurazione facoltativa**.
7. Nel pannello **Creare VM**, fare clic su **Crea**. Si noti il riquadro visualizzato di seguito nel dashboard.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## Come recuperare le informazioni relative all'indirizzo IP privato statico per una VM

Per visualizzare le informazioni dell’indirizzo IP privato statico per la macchina virtuale creata con la procedura descritta sopra, eseguire la procedura seguente.

1. Dal portale di Azure, fare clic su **ESPLORA TUTTO** > **Macchine virtuali (classico)** > **DNS01** > **Tutte le impostazioni** > **Indirizzi IP** e notare l'assegnazione di indirizzi IP e l'indirizzo IP come indicato di seguito.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato dalla macchina virtuale creata in precedenza, attenersi alla procedura seguente.
	
1. Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Dinamica** a destra dell’**assegnazione di indirizzi IP**, poi fare clic su **Salva**, e su **Sì**.

	![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato per la macchina virtuale creata in precedenza, attenersi alla procedura seguente:

1. Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Statico** a destra dell’**assegnazione di indirizzi IP**.
2. Tipo *192.168.1.101* per **indirizzo IP**, poi fare clic su **Salva**, quindi fare clic su **Sì**.

## Passaggi successivi

- Informazioni su [indirizzi IP pubblici riservati](../virtual-networks-reserved-public-ip).
- Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0211_2016-->