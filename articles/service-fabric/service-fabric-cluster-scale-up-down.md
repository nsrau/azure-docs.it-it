<properties
   pageTitle="Aumentare o ridurre le istanze del cluster di Service Fabric | Microsoft Azure"
   description="Aumentare o ridurre le istanze di un cluster di Service Fabric per soddisfare esigenze specifiche aggiungendo o rimuovendo nodi di macchine virtuali."
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
   ms.date="01/29/2016"
   ms.author="chackdan"/>


# Aumentare o ridurre le istanze di un cluster di Service Fabric mediante l'aggiunta o la rimozione di macchine virtuali

È possibile aumentare o ridurre le istanze dei cluster di Azure Service Fabric per soddisfare esigenze specifiche aggiungendo o rimuovendo macchine virtuali.

>[AZURE.NOTE] È necessario che nella sottoscrizione sia incluso un numero di core sufficienti per aggiungere le nuove VM che costituiranno il cluster.

## Aumentare o ridurre le istanze di un cluster di Service Fabric manualmente

### Scegliere il tipo di nodo di cui aumentare o ridurre le istanze

Se il cluster include più tipi di nodo, sarà necessario aggiungere o rimuovere le VM da specifici tipi di nodo. Ecco come:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare a **Cluster di Service Fabric**. ![Pagina dei cluster di Service Fabric nel portale di Azure][BrowseServiceFabricClusterResource]

3. Selezionare il cluster di cui si vuole aumentare o ridurre il numero di istanze.

4. Passare al pannello **Impostazioni** nel dashboard del cluster. Se il pannello **Impostazioni** non viene visualizzato, fare clic su **Tutte le impostazioni** nella parte principale del dashboard del cluster.

5. Fare clic su **Tipi di nodo**. Verrà visualizzato un pannello contenente l'elenco dei tipi di nodo.

6. Fare clic sul tipo di nodo di cui si vuole aumentare o ridurre il numero di istanze. Verrà visualizzato un pannello contenente i dettagli relativi al tipo di nodo.

### Aumentare il numero di istanze mediante l'aggiunta di nodi

Modificare il numero di VM fino a raggiungere la quantità desiderata e salvare. I nodi o le VM vengono aggiunti al termine del processo di distribuzione.

### Ridurre il numero di istanze mediante la rimozione di nodi

La rimozione di nodi è un processo che si articola in due passaggi:

1. Modificare il numero di VM fino a raggiungere la quantità desiderata e salvare. L'estremità inferiore del dispositivo di scorrimento indica il numero minimo di VM necessarie per quel tipo di nodo.

    >[AZURE.NOTE] È necessario conservare almeno 5 VM per il tipo di nodo primario.

2. Al termine del processo di distribuzione verranno comunicati i nomi delle VM che è possibile eliminare. Sarà quindi necessario individuare le risorse delle VM ed eliminarle:

    a. Tornare al dashboard del cluster e fare clic su **Gruppo di risorse**. Verrà visualizzato il pannello **Gruppo di risorse**.

    b. Controllare nell'area **Riepilogo** o aprire l'elenco di risorse facendo clic su "**...**".

    c. Fare clic sul nome della VM che il sistema ha indicato come eliminabile.

    d. Fare clic sull'icona **Elimina** per eliminare la VM.

## Ridimensionare automaticamente i cluster di Service Fabric

Attualmente i Service Fabric Cluster non supportano il ridimensionamento automatico. Nel prossimo futuro i cluster si baseranno su set di scalabilità di macchine virtuali. Questo renderà possibile il ridimensionamento automatico, che avrà un comportamento simile a quello del ridimensionamento automatico disponibile nei servizi cloud.

## Ridimensionare i cluster tramite PowerShell o l'interfaccia della riga di comando

Questo articolo descrive come ridimensionare i cluster tramite il portale. È tuttavia possibile eseguire le stesse azioni dalla riga di comando usando i comandi di Gestione risorse di Azure sulla risorsa del cluster. La risposta del comando GET sulla risorsa del cluster restituisce l'elenco di nodi che sono stati disabilitati.

## Passaggi successivi

- [Informazioni sugli aggiornamenti dei cluster](service-fabric-cluster-upgrade.md)
- [Informazioni sul partizionamento dei servizi con stato per la massima scalabilità](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0204_2016-->