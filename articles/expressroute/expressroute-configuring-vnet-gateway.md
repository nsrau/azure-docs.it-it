<properties
   pageTitle="Configurazione di una rete virtuale per Expressroute | Microsoft Azure"
   description="In questo articolo viene illustrata la configurazione di una rete virtuale (VNet) per ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/21/2015"
   ms.author="cherylmc"/>

#  Configurare una rete virtuale per ExpressRoute

1. Accedere al **portale di gestione**.
2. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.
3. Nella pagina **Dettagli della rete virtuale** immettere le informazioni seguenti: Per ulteriori informazioni sulle impostazioni nella pagina dei dettagli, vedere il [pagina dettagli rete virtuale](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails).

	- **Nome**: assegnare un nome alla rete virtuale. Questo nome della rete virtuale viene usato quando si distribuiscono le macchine virtuali e le istanze PaaS e pertanto è consigliabile non specificare un nome troppo complicato.
	- **Località**: la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che risiedano le risorse (macchine virtuali). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare l’area associata alla rete virtuale dopo averla creata.

4. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la pagina [DNS Servers and VPN Connectivity](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS).

	- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dall'elenco a discesa. Questa impostazione non crea un server DNS, consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale.
	- **Configura una VPN Site-To-Site**: selezionare la casella di controllo **Configura una VPN Site-To-Site**.
	- **Seleziona ExpressRoute** : selezionare la casella di controllo **Usa ExpressRoute**. Questa opzione viene visualizzata solo se è stato selezionato ***Configura una VPN Site-To-Site*** nel passaggio precedente.
	- **Rete locale**: una rete locale rappresenta la posizione fisica locale. È possibile selezionare una rete locale creata in precedenza oppure creare una nuova rete locale.

	Se si seleziona una rete locale esistente, ignorare il passaggio 5.

5. Se si crea una nuova rete locale, verrà visualizzato la pagina **Connettività Site-to-Site**. Se si seleziona una rete locale creata in precedenza, questa pagina non verrà visualizzata nella procedura guidata e sarà possibile passare alla sezione successiva. Per configurare la rete locale, immettere le informazioni seguenti e quindi fare clic sulla freccia avanti. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la pagina [Connettività Site-to-Site](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETSITE).

	- **Nome**: nome da assegnare al sito di rete locale.
	- **Spazio di indirizzi**: inclusi IP iniziale e CIDR (conteggio indirizzi). È possibile specificare qualsiasi intervallo di indirizzi, purché non si sovrapponga all'intervallo di indirizzi della rete virtuale.
	- **Aggiungi spazio di indirizzi**: questa impostazione non è rilevante per ExpressRoute.

6. Nella pagina **Virtual Network Address Spaces** immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete. Esistono diverse regole relative allo spazio degli indirizzi della rete virtuale; è possibile fare riferimento alla pagina [Web dedicata](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS) per ulteriori informazioni.

	- **Spazio di indirizzi**: inclusi IP iniziale e conteggio indirizzi. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.
	- **Aggiungi subnet** inclusi IP iniziale e conteggio indirizzi. Non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP dinamici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze PaaS.
	- **Aggiungi subnet gateway**: fare clic per aggiungere la subnet del gateway. La subnet del gateway viene usata solo per il gateway di rete virtuale ed è obbligatoria per la configurazione. ***Importante:*** la subnet del gateway per ExpressRoute deve essere /28 o superiore.

7. Fare clic sul segno di spunta nella parte inferiore della pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, in **Status** verrà visualizzato **Created** nella pagina relativa alle **Reti** del portale di gestione.

8. Nella pagina relativa alle **Reti**, fare clic sulla rete virtuale appena creata, quindi fare clic su **Dashboard**.
9. Nella parte inferiore della pagina Dashboard, fare clic su **CREATE GATEWAY**, quindi fare clic su **Yes**.

10. All'avvio della creazione del gateway, verrà visualizzato un messaggio che indica che la procedura è stata avviata. La creazione del gateway potrebbe richiedere fino a 15 minuti.
11. **Collegare la rete al circuito.** Procedere con le istruzioni riportate di seguito solo dopo aver confermato che lo stato del circuito è ora il seguente: 

	- ServiceProviderState: Provisioned
	- Status: Enabled

	Verificare che sia disponibile almeno una rete virtuale di Azure con un gateway creato. La subnet del gateway deve essere /28 o superiore per poter usare una connessione ExpressRoute e deve essere attiva e in esecuzione.

			PS C:> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
			Provisioned

## Passaggi successivi
Se si vuole aggiungere macchine virtuali alla rete virtuale, vedere [Come creare una macchina virtuale personalizzata](../virtual-machines-create-custom.md).

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md).

Per altre informazioni sulle reti virtuali, vedere [Domande frequenti su Rete virtuale](https://msdn.microsoft.com/library/azure/dn133803.aspx).

 

<!---HONumber=62-->