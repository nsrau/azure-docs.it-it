<properties
   pageTitle="Ridimensionamento di un Service Fabric Cluster | Microsoft Azure"
   description="Ridimensionare i Service Fabric Cluster per soddisfare esigenze specifiche aggiungendo o rimuovendo macchine virtuali"
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
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# Ridimensionamento di un Service Fabric Cluster mediante l'aggiunta o la rimozione di macchine virtuali (VM)

È possibile ridimensionare i Service Fabric Cluster per soddisfare esigenze specifiche aggiungendo o rimuovendo macchine virtuali.

>[AZURE.NOTE]Si presuppone che la sottoscrizione preveda un numero di core sufficienti per aggiungere le nuove VM che costituiranno questo cluster.


## Ridimensionamento manuale di un Service Fabric Cluster

Se il cluster include più tipi di nodo, sarà necessario aggiungere VM in specifici tipi di nodo o rimuoverle da questi ultimi. È possibile aggiungere VM a un tipo di nodo e rimuoverle da un altro nella stessa distribuzione.

### Aggiornamento di un cluster distribuito tramite il portale

Si tratta di un processo complesso che viene eseguito da un modulo PowerShell caricato in un repository Git.

**Passaggio 1**: copiare la cartella nel computer da questo [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Passaggio 2**: assicurarsi che nel computer sia installato Azure SDK 1.0 o versione successiva.

**Passaggio 3**: aprire una finestra di PowerShell e importare il file ServiceFabricRPHelpers.psm.

```
Remove-Module ServiceFabricRPHelpers
```

Importare il modulo PowerShell appena copiato. È possibile copiare solo il comando seguente e sostituire il percorso con quello del file con estensione psm1 nel computer.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **Passaggio 4**: accedere con il proprio account Azure

```
Login-AzureRmAccount
```

Eseguire il comando Invoke-ServiceFabricRPClusterScaleUpgrade e assicurarsi che il nome del gruppo di risorse e la sottoscrizione di cui si dispone siano corretti.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

Di seguito è riportato un esempio compilato dello stesso comando di PowerShell.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **Passaggio 5**: il comando recupera ora informazioni sul cluster ed esamina tutti i tipi di nodo, prima specificando il numero di VM/nodi esistenti per quel tipo di nodo e quindi chiedendo di specificare quale deve essere il nuovo numero di VM/nodi.

 **Per aumentare il numero di istanze di questo tipo di nodo**, specificare una quantità maggiore di macchine virtuali rispetto al numero attuale.

**Per ridurre il numero di istanze di questo tipo di nodo**, specificare una quantità minore di VM rispetto al numero attuale.

Verranno esaminati in ciclo tutti i tipi di nodo. Se il cluster dispone di un solo tipo di nodo, verrà visualizzato un solo prompt.
 
  **Passaggio 6**: se si aggiungono nuove VM, verrà visualizzato un prompt in cui sarà necessario specificare l'ID utente e la password di desktop remoto per le VM che si stanno aggiungendo.
 
**Passaggio 7**: nella finestra di output vengono ora visualizzati dei messaggi con cui si informa l'utente dello stato di avanzamento della distribuzione. Al termine del processo di distribuzione, il cluster deve avere il numero di nodi specificato nel passaggio 5.


![Aumento/diminuzione uscita PS][ScaleupDownPSOut]


**Passaggio 8**: se era stata eseguita una richiesta di riduzione del numero di istanze, è necessario eseguire un passaggio aggiuntivo per l'eliminazione di VM. Lo script disattiva tutte le VM da rimuovere, sulle quali quindi non saranno più disponibili repliche di applicazioni o sistemi. È ora possibile eliminare queste VM.

**NOTA**: anche se i nodi disattivati non vengono più usati dal cluster di Service Fabric, è comunque necessario eliminare le VM disattivate, in modo che non vengono addebitate.

**Passaggio 8.1**: accedere al portale di Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

**Passaggio 8.2**: selezionare la risorsa cluster di cui si sta riducendo il numero di istanze e fare clic su "Tutte le impostazioni" nella Web part Essentials.

**Passaggio 8.3**: fare clic su Tipi di nodo per visualizzare l'elenco dei nodi disattivati. In questa immagine, chackodnt15, chackodnt24, chackodnt25 e chackodnt26 sono le VM che è ora necessario eliminare.

![Elenco nodi disattivati][DeactivatedNodeList]

**Passaggio 8.4**: eliminare le VM tramite PowerShell o il portale. Con questa eliminazione termina il processo di riduzione del numero di istanze del cluster.

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
Di seguito è riportato un esempio compilato del comando

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### Aggiornamento di un cluster distribuito tramite ARM, PowerShell o l'interfaccia della riga di comando

Se il cluster era stato originariamente distribuito usando un modello di ARM, è necessario modificare il numero di VM relative a un determinato tipo di nodo, nonché tutte le risorse che supportano la VM. Il numero minimo di VM per un tipo di nodo primario è 5 (per i cluster non di test), mentre per i cluster di test è 3.

Dopo aver creato il nuovo modello, è ora possibile distribuirlo tramite ARM usando lo stesso gruppo di risorse del cluster che si sta aggiornando.


## Ridimensionamento automatico del Service Fabric Cluster

Attualmente i Service Fabric Cluster non supportano il ridimensionamento automatico. Nel prossimo futuro i cluster si baseranno su set di scalabilità di macchine virtuali. Ciò renderà possibile il ridimensionamento automatico, che avrà un comportamento simile a quello del ridimensionamento automatico disponibile nei servizi cloud.


## Passaggi successivi

- [Informazioni sugli aggiornamenti dei cluster](service-fabric-cluster-upgrade.md)
- [Informazioni sul partizionamento dei servizi con stato per la massima scalabilità](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->