---
title: Ciclo di vita di attivazione e disattivazione di Azure Service Fabric
description: Illustra il ciclo di vita dell'applicazione e ServicePackage in un nodo
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: da395f020cf3d299211427e740975764653b487d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85802279"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Ciclo di vita dell'hosting di Azure Service Fabric
Questo articolo fornisce una panoramica degli eventi che si verificano quando un'applicazione viene attivata in un nodo e in varie configurazioni del cluster usate per controllare il comportamento.

Prima di procedere oltre, assicurarsi di avere compreso i diversi concetti e relazioni illustrati in [Modellare un'applicazione in Service Fabric][a1]. 

> [!NOTE]
> In questo articolo, se non diversamente specificato:
>
> - La *replica* si riferisce sia a una replica di un servizio con stato che a un'istanza di un servizio senza stato.
> - *CodePackage* viene considerato equivalente a un processo *ServiceHost* che registra un *serviceType*e ospita le repliche dei servizi di tale *serviceType*.
>

## <a name="activation-of-applicationservicepackage"></a>Attivazione di Application/ServicePackage

La pipeline per l'attivazione è la seguente:

1. Scaricare pacchetto dell'applicazione. Ad esempio: ApplicationManifest.xml e così via.
2. Configurare l'ambiente per l'applicazione, ad esempio, creare utenti e così via.
3. Avviare la verifica dell'applicazione per la disattivazione.
4. Scaricare ServicePackage. Ad esempio: ServiceManifest.xml, codice, configurazione, pacchetti di dati e così via.
5. Configurare l'ambiente per il pacchetto del servizio per esempio: configurare il firewall, allocare le porte per gli endpoint e così via.
6. Avviare il rilevamento di ServicePackage per la disattivazione.
7. Avviare SetupEntryPoint di codepackages e attendere il completamento.
8. Avviare MainEntryPoint di codepackages.

### <a name="servicetype-blocklisting"></a>Blocklisting ServiceType
**ServiceTypeDisableFailureThreshold** determina il numero di errori (attivazione, download, errori di CodePackage) dopo i quali serviceType è pianificato per Blocklisting. Quindi, il primo errore di attivazione/download, o l'arresto anomalo di CodePackage, deve attivare la pianificazione di ServiceType Blocklisting. La configurazione di **ServiceTypeDisableGraceInterval** determina l'intervallo di tolleranza dopo il quale ServiceType viene infine contrassegnato come Blocklisting in tale nodo. Si noti che, per tutto questo, l'attivazione/download/il riavvio del CodePackage dovrebbe essere ancora in modalità di ripetizione interna e rilevata dal sottosistema di hosting. Con un nuovo tentativo, ad esempio, verrà pianificato il riavvio di CodePackage dopo l'arresto anomalo o Service Fabric tenterà di scaricare nuovamente i pacchetti.
Una volta Blocklisting, viene visualizzato un errore che segnala l'errore "System. Hosting" per la proprietà "ServiceTypeRegistration: ServiceType". Il ServiceType è stato disabilitato nel nodo ".

ServiceType verrà abilitato nuovamente nel nodo 
- Se l'operazione di attivazione riesce o raggiunge **ActivationMaxFailureCount** tentativi in caso di errore.
- Se l'operazione di download riesce o raggiunge **DeploymentMaxFailureCount** tentativi in caso di errore.
- Se viene eseguito il backup di un CodePackage arrestato in modo anomalo e viene registrato correttamente il ServiceType.

Il motivo per cui si Abilita nuovamente il **ActivationMaxFailureCount**ServiceType dopo i / tentativi**DeploymentMaxFailureCount** di ActivationMaxFailureCount è il numero massimo di tentativi Service Fabric eseguirà per attivare/scaricare un'applicazione in un nodo. In caso contrario, l'operazione corrente non viene ritentata e poiché Service Fabric desidera fornire al servizio un'altra opportunità per l'attivazione, che potrebbe avere esito positivo, causando il problema di correzione automatica, è associato al ciclo di vita dell'operazione di attivazione/download. Una nuova operazione di attivazione/download attivata dal posizionamento di una replica può attivare di nuovo il Blocklisting ServiceType o può avere esito positivo.

> [!NOTE]
> Se il CodePackage che non registra un ServiceType si arresta in modo anomalo, non ha alcun effetto sul ServiceType. Il ServiceType viene influenzato solo dal CodePackage che ospita un arresto anomalo della replica.
>

### <a name="codepackage-crash"></a>Arresto anomalo di CodePackage
Quando si verifica un arresto anomalo di un pacchetto di codice, Service Fabric utilizza un back-off per riavviarlo e il back-off è indipendente dal fatto che il pacchetto di codice abbia registrato un tipo con US o meno.

Il valore di back-off è sempre min (RetryTime, **ActivationMaxRetryInterval**) e questo valore può essere costante, lineare o esponenziale in base alla configurazione di **ActivationRetryBackoffExponentiationBase** .

- Costante: se **ActivationRetryBackoffExponentiationBase** = = 0 Then RetryTime = **ActivationRetryBackoffInterval**;
- Linear: se **ActivationRetryBackoffExponentiationBase** = = 0 Then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** dove ContinousFailureCount è il numero di volte in cui un CodePackage si arresta o non viene attivato.
- Esponenziale: RetryTime = (**ActivationRetryBackoffInterval** in secondi) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
È possibile controllare il comportamento desiderato, ad esempio i riavvii rapidi. Parliamo di Linear. Ciò significa che se si verifica un arresto anomalo di un CodePackage, l'intervallo di avvio sarà dopo 10, 20, 30 40 sec fino alla disattivazione di CodePackage. 
    
La quantità massima di tempo Service Fabric il backup (attese) dopo che un errore è governato da **ActivationMaxRetryInterval**
    
Se il CodePackage si arresta in modo anomalo e viene riavviato, deve rimanere attivo per **CodePackageContinuousExitFailureResetInterval** per Service Fabric considerarlo come integro a questo punto, sovrascriverà il report sull'integrità come OK e ripristinerà il ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage che non registra ServiceType
Se un CodePackage rimane attivo e si prevede di registrare un ServiceType con Microsoft, ma mai, in tal caso Service Fabric genererà un avviso HealthReport dopo **ServiceTypeRegistrationTimeout** per indicare che ServiceType non è stato configurato entro il timeout.

### <a name="activation-failure"></a>Errore di attivazione
Service Fabric utilizza sempre un back-off lineare (uguale all'arresto anomalo di CodePackage) quando viene rilevato un errore durante l'attivazione. Ciò significa che l'operazione di attivazione verrà restituita dopo (0 + 10 + 20 + 30 + 40) = 100 sec (il primo tentativo è immediato). Dopo che l'attivazione non è stata ritentata.
    
Max Activation backoff può essere **ActivationMaxRetryInterval** e riprovare **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Errore di download
Service Fabric usa sempre un back-off lineare quando rileva un errore durante il download. Ciò significa che l'operazione di attivazione verrà restituita dopo (0 + 10 + 20 + 30 + 40) = 100 sec (il primo tentativo è immediato). Al termine del download non verrà eseguito un nuovo tentativo. Il back-off lineare per il download è uguale a ContinuousFailureCount ***DeploymentRetryBackoffInterval** e può essere impostato su **DeploymentMaxRetryInterval**. Analogamente alle attivazioni, l'operazione di download può ritentare per **ActivationMaxFailureCount**.

> [!NOTE]
> Prima di modificare le configurazioni, di seguito sono riportati alcuni esempi da tenere presenti.

* Se il CodePackage continua a bloccarsi e viceversa, ServiceType verrà disabilitato. Tuttavia, se la configurazione delle attivazioni è tale da consentire un riavvio rapido, è possibile che il CodePackage venga aggiornato per alcune volte prima di poter visualizzare la disabilitazione di ServiceType. Per esempio: si supponga che il CodePackage venga visualizzato, registra il ServiceType con Service Fabric e quindi si arresta in modo anomalo. In tal caso, una volta che l'hosting riceve una registrazione del tipo, il periodo **ServiceTypeDisableGraceInterval** viene annullato. Questa operazione può essere ripetuta finché il CodePackage non torna a un valore maggiore di **ServiceTypeDisableGraceInterval** e quindi ServiceType verrà disabilitato nel nodo. Quindi, può trattarsi di un po' prima che il ServiceType venga disabilitato nel nodo.

* In caso di attivazione, quando Service Fabric sistema deve inserire una replica in un nodo, RA (ReconfigurationAgent) chiede al sottosistema di hosting di attivare l'applicazione e ritenta la richiesta di attivazione ogni 15 sec (**RAPMessageRetryInterval**). Per Service Fabric sistema in grado di capire che ServiceType è stato disabilitato, l'operazione di attivazione nell'hosting deve risiedere per un periodo più lungo di intervallo tra tentativi e **ServiceTypeDisableGraceInterval**. Ad esempio: lasciare che il cluster abbia le configurazioni **ActivationMaxFailureCount** impostate su 5 e **ActivationRetryBackoffInterval** impostato su 1 sec. Ciò significa che l'operazione di attivazione verrà restituita dopo (0 + 1 + 2 + 3 + 4) = 10 sec (il primo tentativo è immediato) e dopo che l'host ha ritentato il tentativo. In questo caso, l'operazione di attivazione verrà completata e non verrà eseguito un nuovo tentativo dopo 15 secondi. Il problema è dovuto al fatto che Service Fabric esaurito tutti i tentativi entro 15 secondi. Quindi, ogni tentativo da ReconfigurationAgent crea una nuova operazione di attivazione nel sottosistema di hosting e il modello continuerà a essere ripetuto e ServiceType non verrà mai disabilitato nel nodo. Poiché il ServiceType non viene disabilitato nel componente FM del sistema node SF (FailoverManager), la replica non viene spostata in un nodo diverso.
> 

## <a name="deactivation"></a>Disattivazione

Quando un ServicePackage viene attivato in un nodo, viene rilevato per la disattivazione. Deactivator è l'entità che ne tiene traccia.
Il Deactivator funziona in due modi:

1.  Periodicamente: a ogni **DeactivationScanInterval**verifica la presenza di ServicePackages, che non hanno mai ospitato una replica e li contrassegna come candidati per la disattivazione.
2.  ReplicaClose: se una replica è chiusa, Deactivator ottiene un DecrementUsageCount. Se il conteggio va a 0, significa che il ServicePackage non ospita alcuna replica e pertanto è un candidato per la disattivazione.

 In base alla modalità di attivazione [esclusiva/condivisa][a2], i candidati per la disattivazione vengono pianificati dopo **DeactivationGraceInterval** per SharedMode/ **ExclusiveModeDeactivationGraceInterval** per ExclusiveMode. Se è presente all'interno di questo periodo di tempo, viene annullata una nuova posizione di replica.

### <a name="periodically"></a>Periodicamente
Esempio 1: si può dire che la disattivazione esegue un'analisi al momento T (**DeactivationScanInterval**). L'analisi successiva sarà a 2T. Si supponga che l'attivazione di ServicePackage sia stata eseguita a T + 1. Questo ServicePackage non ospita una replica e pertanto deve essere disattivato. Affinché il ServicePackage sia un candidato per la disattivazione, è necessario che sia nello stato di nessuna replica per almeno T tempo. Ciò significa che sarà idoneo per la disattivazione a 2T + 1. Quindi, l'analisi in 2T non troverà questo ServicePackage come candidato per la disattivazione. Il successivo ciclo di disattivazione 3T pianifica questo ServicePackage per la disattivazione, perché ora non è in stato di replica per il tempo T.  

Esempio 2: Supponiamo che un ServicePackage venga attivato all'ora T-1 e disactivator esegue un'analisi a T. Il ServicePackage non ospita una replica. Quindi, al successivo Scan 2T questo ServicePackage verrà trovato come candidato per la disattivazione e quindi verrà pianificato per la disattivazione.  

Esempio 3: Supponiamo che un ServicePackage venga attivato a T-1 e disactivator esegue un'analisi a T. Il ServicePackage non ospita ancora una replica. Ora in T + 1 viene posizionata una replica, ovvero L'hosting ottiene un IncrementUsageCount, il che significa che viene creata una replica. A questo punto, a 2T questo ServicePackage non verrà pianificato per la disattivazione. A questo punto, la disattivazione passerà alla logica ReplicaClose illustrata di seguito.

Esempio 4: Supponiamo che il ServicePackage sia di grandi dimensioni, come 10 GB, quindi il download del nodo potrebbe richiedere tempo. Una volta attivata l'applicazione, il disattivatore tiene traccia del ciclo di vita. A questo punto, se si ha la configurazione **DeactivationScanInterval** di dimensioni ridotte, è possibile che si verifichino problemi nel caso in cui il ServicePackage non stia ricevendo tempo per l'attivazione sul nodo perché è stato eseguito il download. Per risolvere il problema, è possibile eseguire il [pre-download di ServicePackage nel nodo][p1]. 

> [!NOTE]
> Quindi, un ServicePackage può essere disattivato ovunque tra (**DeactivationScanInterval** a 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Chiusura replica:
Disactivator mantiene il numero di repliche che un ServicePackage include. Se un ServicePackage contiene una replica e la replica è chiusa/inattiva, l'hosting ottiene un DecrementUsageCount. Quando una replica viene aperta, l'hosting ottiene un IncrementUsageCount. Il decremento significa che ServicePackage sta ospitando una replica meno e quando il conteggio scende a 0, il ServicePackage è pianificato per la disattivazione e l'ora dopo la quale verrà disattivata è **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Per esempio, si può dire che un decremento si verifica a T e ServicePackage è pianificato per la disattivazione a 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Se durante questo periodo di hosting ottiene un IncrementUsage che significa che viene creata una replica, la disattivazione viene annullata.

> [!NOTE]
>Quindi, cosa significa fondamentalmente: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: il tempo assegnato a un ServicePackage per ospitare nuovamente un'altra replica dopo che è stata ospitata una replica. 
**DeactivationScanInterval**: tempo minimo concesso a ServicePackage per ospitare una replica se non è mai stata ospitata alcuna replica, ad esempio Se non viene utilizzato.
>

### <a name="ctrlc"></a>CTRL+C
Quando un ServicePackage passa il **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** DeactivationGraceInterval e non ospita ancora una replica, la disattivazione non è annullabile. CodePackage viene emesso un gestore CTRL + C che indica che ora la pipeline di disattivazione deve passare per arrestare il processo. Durante questo periodo di tempo, se una nuova replica per lo stesso ServicePackage sta provando a essere posizionata, avrà esito negativo perché non è possibile passare dalla disattivazione all'attivazione.

## <a name="cluster-configs"></a>Configurazioni del cluster

Configurazioni con impostazioni predefinite che influiscano sull'attivazione/decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: valore predefinito 1. Soglia per il conteggio degli errori dopo la quale FM (FailoverManager) riceve una notifica per disabilitare il tipo di servizio su tale nodo e provare un nodo diverso per la selezione host.
**ServiceTypeDisableGraceInterval**: valore predefinito di 30 secondi. intervallo di tempo dopo il quale il tipo di servizio può essere disabilitato.
**ServiceTypeRegistrationTimeout**: valore predefinito 300 sec. Timeout per la registrazione del ServiceType con Service Fabric.

### <a name="activation"></a>Activation
**ActivationRetryBackoffInterval**: valore predefinito di 10 sec. backoff per ogni errore di attivazione.
**ActivationMaxFailureCount**: valore predefinito 20. Numero massimo di tentativi di attivazione del sistema non riusciti prima di rinunciare. 
**ActivationRetryBackoffExponentiationBase**: valore predefinito 1,5.
**ActivationMaxRetryInterval**: valore predefinito 3600 sec. numero massimo di interruzioni per l'attivazione in caso di errori.
**CodePackageContinuousExitFailureResetInterval**: valore predefinito 300 sec. Timeout per la reimpostazione del conteggio degli errori di uscita continua per CodePackage.

### <a name="download"></a>Scarica
**DeploymentRetryBackoffInterval**: valore predefinito 10. Intervallo di backoff per l'errore di distribuzione.
**DeploymentMaxRetryInterval**: valore predefinito 3600 sec. numero massimo di interruzioni per la distribuzione in caso di errori.
**DeploymentMaxFailureCount**: valore predefinito 20. Verranno eseguiti altri tentativi di distribuzione dell'applicazione per le volte specificate in DeploymentMaxFailureCount prima di considerare non riuscita la distribuzione dell'applicazione nel nodo.

### <a name="deactivation"></a>Disattivazione
**DeactivationScanInterval**: valore predefinito 600 sec. tempo minimo assegnato a ServicePackage per ospitare una replica se non è mai stata ospitata alcuna replica, ad esempio Se non viene utilizzato.
**DeactivationGraceInterval**: valore predefinito 60 sec. Tempo assegnato a un ServicePackage per ospitare nuovamente un'altra replica dopo aver ospitato una replica in caso di modello di processo **condiviso** .
**ExclusiveModeDeactivationGraceInterval**: valore predefinito di 1 sec. Tempo assegnato a un ServicePackage per ospitare nuovamente un'altra replica dopo aver ospitato una replica in caso di modello di processo **esclusivo** .

## <a name="next-steps"></a>Passaggi successivi
[Creare il pacchetto di un'applicazione][a3] e prepararlo per la distribuzione.

[Distribuire e rimuovere applicazioni][a4]. Questo articolo descrive come usare PowerShell per gestire le istanze dell'applicazione.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/copy-servicefabricservicepackagetonode
