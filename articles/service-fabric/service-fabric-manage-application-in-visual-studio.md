---
title: Gestire le applicazioni in Visual Studio | Microsoft Docs
description: "Usare Visual Studio per creare, sviluppare, creare pacchetti, distribuire ed effettuare il debug di applicazioni dell’infrastruttura di servizi e di servizi."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: 81814ef07cfab06855b3bfbf0eb6ef51b8dfce4f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni dell’infrastruttura di servizi
È possibile gestire le applicazioni e i servizi di Service Fabric di Azure tramite Visual Studio. Dopo aver [configurato l'ambiente di sviluppo](service-fabric-get-started.md), è infatti possibile usare Visual Studio per creare applicazioni di Service Fabric, aggiungere servizi o creare i pacchetti, registrare e distribuire le applicazioni nel cluster di sviluppo locale.

## <a name="deploy-your-service-fabric-application"></a>Distribuire l'applicazione di Service Fabric
Per impostazione predefinita, la distribuzione di un'applicazione combina in un'unica operazione i passaggi seguenti:

1. Creazione del pacchetto applicazione
2. Caricamento del pacchetto applicazione in Image Store
3. Registrazione del tipo di applicazione
4. Rimozione delle eventuali istanze dell'applicazione in esecuzione
5. Creazione di un'istanza dell'applicazione

In Visual Studio se si preme **F5** l'applicazione viene distribuita e il debugger viene allegato a tutte le istanze dell'applicazione. È possibile utilizzare **Ctrl + F5** per distribuire un'applicazione senza il debug, o pubblicare in un cluster locale o remoto mediante il profilo di pubblicazione. 

### <a name="application-debug-mode"></a>Modalità di debug applicazione
Visual Studio include la proprietà **Modalità di debug applicazione** che controlla la modalità di distribuzione dell'applicazione nel contesto del debug.

#### <a name="to-set-the-application-debug-mode-property"></a>Per impostare la proprietà Modalità di debug applicazione
1. Nel menu di scelta rapida del progetto applicazione Service Fabric (*.sfproj) scegliere **Proprietà** (o premere **F4**).
2. Nella finestra **Proprietà** impostare la proprietà **Modalità di debug applicazione**.

![Impostare la proprietà Modalità di debug applicazione][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modalità di debug dell'applicazione

1. **Aggiorna l'applicazione**: questa modalità consente di modificare rapidamente il codice e di eseguirne il debug e supporta la modifica dei file Web statici durante il debug. Questa modalità funziona solo se il cluster di sviluppo locale è in [modalità a 1 nodo](/service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Rimuovi applicazione** fa sì che l'applicazione venga rimossa al termine della sessione di debug.
3. **Aggiornamento automatico**: l'applicazione resta in esecuzione al termine della sessione di debug. La sessione di debug successiva considera la distribuzione come un aggiornamento. Il processo di aggiornamento mantiene tutti i dati immessi in una sessione di debug precedente.
4. **Mantieni l'applicazione**: l'applicazione resta in esecuzione nel cluster al termine della sessione di debug. All'inizio della sessione di debug successiva, l'applicazione viene rimossa.

Con l'opzione **Aggiornamento automatico** i dati vengono mantenuti applicando le funzionalità di aggiornamento dell'applicazione di Service Fabric. Per altre informazioni sull'aggiornamento delle applicazioni e su come è possibile eseguire un aggiornamento in un ambiente reale, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Aggiungere un servizio all'applicazione di Service Fabric
È possibile aggiungere nuovi servizi all'applicazione per estenderne le funzionalità.  Per essere certi che il servizio venga incluso nel pacchetto applicazione, aggiungere il servizio usando la voce di menu **New Fabric Service** .

![Aggiungere un nuovo servizio di Service Fabric][newservice]

Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione e specificare un nome per il servizio.  Per decidere più facilmente quale tipo di servizio usare, vedere [Scelta di un framework per il servizio](service-fabric-choose-framework.md) .

![Selezionare un tipo di progetto di servizio Service Fabric da aggiungere all'applicazione][addserviceproject]

Il nuovo servizio viene aggiunto alla soluzione e al pacchetto dell'applicazione esistente. I riferimenti e un'istanza del servizio predefinita vengono aggiunti al manifesto dell'applicazione, in modo che il servizio venga creato e avviato alla successiva distribuzione dell'applicazione.

![Il nuovo servizio viene aggiunto al manifesto dell'applicazione][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Creazione del pacchetto per l'applicazione di Service Fabric
Per distribuire l'applicazione e i relativi servizi in un cluster, è necessario creare un pacchetto dell’applicazione.  Il pacchetto organizza il manifesto dell'applicazione, i manifesti dei servizi e gli altri file necessari in un layout specifico.  Visual Studio imposta e gestisce il pacchetto nella cartella del progetto dell'applicazione, nella cartella "pkg".  Facendo clic su **Pacchetto** dal menu di scelta rapida **Applicazione** il pacchetto dell'applicazione viene creato o aggiornato.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Rimuovere applicazioni e tipi di applicazione con Cloud Explorer
In Visual Studio è possibile eseguire operazioni di gestione dei cluster di base con Cloud Explorer, che può essere avviato dal menu **Visualizza** . È ad esempio possibile eliminare applicazioni e annullare il provisioning di tipi di applicazione in cluster locali o remoti.

![Rimuovere un'applicazione][removeapplication]

> [!TIP]
> Per funzionalità di gestione dei cluster più avanzate, vedere [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* [Modello di applicazione di Service Fabric](service-fabric-application-model.md)
* [Distribuzione di un'applicazione di Service Fabric](service-fabric-deploy-remove-applications.md)
* [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debug dell'applicazione di Service Fabric](service-fabric-debugging-your-application.md)
* [Visualizzazione del cluster con l’explorer di Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png