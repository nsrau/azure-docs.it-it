<properties 
   pageTitle="Come eliminare una rete virtuale"
	description="Informazioni per eliminare una rete virtuale esistente"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/29/2015"
	ms.author="telmos"/>

# Come eliminare una rete virtuale

Per eliminare una rete virtuale, non è possibile semplicemente fare clic su **Elimina**. È necessario prima effettuare alcune operazioni:

1. **Salvare le impostazioni (facoltativo)**: se si intende salvare le impostazioni della rete virtuale in un file locale, esportare il file di configurazione prima di eliminare la rete virtuale. Per altre informazioni, vedere [Esportare le impostazioni della rete virtuale in un file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Salvando le impostazioni è possibile ricreare la rete virtuale in futuro, se necessario.

1. **Eliminare il gateway della rete virtuale**: se è stato configurato un gateway per la rete virtuale, è necessario eliminarlo prima di eliminare la rete virtuale. A tale scopo, passare alla pagina Dashboard della rete virtuale. Nella parte inferiore della pagina fare clic su **Elimina gateway**.
						
	Potrebbe essere necessario attendere 5-10 minuti l'eliminazione del gateway prima di continuare con i passaggi successivi.

1. **Eliminare i servizi cloud, i siti Web e le macchine virtuali**: se sono stati distribuiti oggetti nella rete virtuale, sarà necessario eliminarli prima di eliminare la rete virtuale.

1. **Eliminare la rete virtuale**: fare clic a destra della riga *Nome* per evidenziare la rete virtuale da eliminare e quindi fare clic su **Elimina** nella parte inferiore della pagina. Seguire le istruzioni visualizzate.

1. **Altre operazioni**: potrebbe anche essere necessario scegliere di eliminare impostazioni di rete locali, server DNS e il gruppo di affinità dopo aver eliminato la rete virtuale.
 

<!---HONumber=August15_HO9-->