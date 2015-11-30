<properties
   pageTitle="Ridimensionamento di un cluster di infrastruttura di servizi | Microsoft Azure"
   description="Ridimensionare i cluster di infrastruttura di servizi per soddisfare esigenze specifiche aggiungendo o rimuovendo macchine virtuali"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Ridimensionamento di un cluster di infrastruttura di servizi mediante l'aggiunta o la rimozione di macchine virtuali (VM)

È possibile ridimensionare i cluster di infrastruttura di servizi per soddisfare esigenze specifiche aggiungendo o rimuovendo macchine virtuali.

>[AZURE.NOTE]Si presuppone che la sottoscrizione preveda un numero di core sufficienti per aggiungere le nuove VM che costituiranno questo cluster.


## Ridimensionamento manuale di un cluster di infrastruttura di servizi

### Scelta del tipo di nodo da ridimensionare

Se il cluster dispone di più tipi di nodo, sarà necessario aggiungere o rimuovere le VM da tipi di nodo specifici.

1. Accedere al portale di Azure all'indirizzo [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Passare al cluster di infrastruttura di servizi ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]

3. Selezionare il cluster che si desidera ridimensionare.

4. Passare al pannello delle impostazioni nel dashboard del cluster. Se non viene visualizzato il pannello delle impostazioni, fare clic su "Tutte le impostazioni" nella parte essenziale nel dashboard del cluster.

5. Fare clic su NodeTypes. Verrà aperto il pannello contenente l'elenco di tipi di nodo.

7. Fare clic sul tipo di nodo che si desidera ridimensionare. Verrà aperto il pannello contenente i dettagli relativi al tipo di nodo.

### Ridimensionamento mediante l'aggiunta di nodi

Modificare il numero di VM aggiungendo macchine virtuali fino a raggiungere il numero desiderato e quindi salvare. I nodi/VM verranno aggiunti al completamento della distribuzione.

### Ridimensionamento mediante la rimozione di nodi

La rimozione di nodi è un processo che si articola in due passaggi:

1. Modificare il numero di VM fino a raggiungere il numero desiderato e quindi salvare. L'estremità inferiore del dispositivo di scorrimento indica il requisito minimo relativo al numero di macchine virtuali per il tipo di nodo specifico.

  >[AZURE.NOTE]È necessario conservare un minimo di 5 macchine virtuali per il tipo di nodo primario.

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. Tornare al dashboard del cluster e fare clic sul gruppo di risorse. Verrà aperto il pannello relativo al gruppo di risorse.

3. Controllare nell'area Riepilogo o aprire l'elenco di risorse facendo clic su "..."

4. Fare clic sul nome della macchina virtuale che il sistema indica come eliminabile.

5. Fare clic sull'icona Elimina per eliminare la macchina virtuale.

## Ridimensionamento automatico del cluster di infrastruttura di servizi

Attualmente i cluster di infrastruttura di servizi non supportano il ridimensionamento automatico. Nel prossimo futuro i cluster si baseranno su set di scalabilità di macchine virtuali. Ciò renderà possibile il ridimensionamento automatico, che avrà un comportamento simile a quello del ridimensionamento automatico disponibile nei servizi cloud.

## Ridimensionamento mediante PowerShell/CLI

Questo articolo descrive come ridimensionare i cluster tramite il portale. Tuttavia, è possibile eseguire le stesse azioni dalla riga di comando usando i comandi ARM sulla risorsa del cluster. La risposta del comando GET sulla risorsa del cluster restituisce l'elenco di nodi che sono stati disabilitati.

## Passaggi successivi

- [Informazioni sugli aggiornamenti dei cluster](service-fabric-cluster-upgrade.md)
- [Informazioni sul partizionamento dei servizi con stato per la massima scalabilità](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->