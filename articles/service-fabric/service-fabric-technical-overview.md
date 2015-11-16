<properties
   pageTitle="Panoramica tecnica dell’infrastruttura di servizi | Microsoft Azure"
   description="Informazioni tecniche su Infrastruttura di servizi. Vengono illustrati i concetti chiave e i cenni preliminari sull'architettura"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2015"
   ms.author="mfussell"/>

# Informazioni tecniche su Infrastruttura di servizi

Infrastruttura di servizi è una piattaforma di sistemi distribuiti che consente di compilare applicazioni scalabili, affidabili, a bassa latenza e facilmente gestibili per il cloud. Ciò significa che è possibile concentrarsi sulle esigenze aziendali e fare in modo che Infrastruttura di servizi si preoccupi di garantire che l'applicazione sia sempre disponibile e scalabile.

## Concetti chiave

**Cluster** - Un set di macchine fisiche o virtuali connesse tramite rete in cui vengono distribuite le istanze dell'applicazione. I cluster possono supportare migliaia di macchine.

**Nodo** - Un'unità indirizzabile in un cluster. I nodi dispongono di caratteristiche quali, ad esempio, proprietà di posizionamento e ID univoci. I nodi possono unirsi a un cluster e correlarsi a un'istanza del sistema operativo con Fabric.exe in esecuzione.

**Applicazione/Tipo di applicazione** - Una raccolta di microservizi. Un tipo di applicazione può essere paragonato a un contenitore per uno o più tipi di servizio. Consultare l'articolo sul [modello di applicazione](service-fabric-application-model.md) per comprendere in che modo un cluster (che a sua volta è costituito da più nodi) può comprendere più ApplicationTypes.

**Servizio/Tipo di servizio** - Codice e configurazione che esegue una funzione autonoma (può avviarsi e funzionare in modo indipendente), ad esempio, un servizio di accodamento o un servizio di database. Un ApplicationType può comprendere uno o più ServiceTypes. Esistono due tipi di servizi:

- Servizio senza stato: un servizio con uno stato dove lo stato è persistente nell'archiviazione esterna, ad esempio l'archiviazione dei database o tabelle di Azure. Se un nodo in cui è attiva un'istanza di questo servizio si arresta, un'altra istanza viene avviata automaticamente in un altro nodo.

- Servizio con stato: un servizio con stato e che raggiunge l'affidabilità tramite la replica tra le repliche in altri nodi del cluster. I servizi con stato dispongono di repliche primarie e più repliche secondarie. Se un nodo in cui è attiva una replica di questo servizio si arresta, viene avviata una nuova replica in un altro nodo e se si trattava della replica primaria, una replica secondaria viene promossa automaticamente a primaria.

**Istanza dell'applicazione** - In un cluster, è possibile creare molte istanze di applicazione di un tipo di applicazione, ognuna delle quali ha un nome specifico. Ogni istanza dell'applicazione può essere gestita e versionata in modo indipendente da altre istanze dell'applicazione dello stesso tipo o di un tipo diverso. Inoltre, definiscono l'isolamento delle risorse e della sicurezza.

**Istanza del servizio** - Codice istanziato per un tipo di servizio. Ogni istanza del servizio ha un nome univoco che inizia con `fabric:/` ed è associato a un'istanza di applicazione con un nome particolare.

**Pacchetto dell'applicazione** - La raccolta di pacchetti di codice del servizio e dei file di configurazione combinati per una determinata applicazione. Si tratta di file fisici che vengono distribuiti e semplicemente di un layout di formato file e cartelle. Ad esempio, un pacchetto di applicazione per un'applicazione di posta elettronica può contenere un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database.

**Modelli di programmazione** - In Infrastruttura di servizi esistono due modelli di programmazione per creare applicazioni:

- Servizi affidabili - Un'API per compilare servizi con e senza stato basati su classi .NET `StatelessService` e `StatefulService`, archiviazione dello stato in raccolte affidabili .NET (dizionario e coda) e con la possibilità di inserire un'ampia gamma di stack di comunicazione, ad esempio l'API Web e WCF. Questo modello di programmazione è adatto per applicazioni in cui è necessario eseguire il calcolo in più unità di stato.

- Attori affidabili - Un'API per creare oggetti con e senza stato tramite il modello di programmazione Actor virtuale adatto per applicazioni con più unità indipendenti di stato e calcolo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
Per ulteriori informazioni su Infrastruttura di servizi, vedere:

- [Modello applicativo](service-fabric-application-model.md)
- [Scenari applicativi](service-fabric-application-scenarios.md)
 

<!---HONumber=Nov15_HO2-->