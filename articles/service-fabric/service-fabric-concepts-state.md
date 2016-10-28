<properties
   pageTitle="Definizione e gestione dello stato | Microsoft Azure"
   description="Come definire e gestire lo stato di un servizio in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# Stato del servizio
Per **stato del servizio** si intendono i dati di cui il servizio necessita per funzionare. Si tratta delle strutture e delle variabili di dati che vengono lette e scritte dal servizio per il funzionamento.

Si consideri ad esempio un semplice servizio calcolatrice. Questo servizio accetta due numeri per restituirne la somma. Si tratta di un servizio puramente senza stato, senza dati associati.

Si consideri ora la stessa calcolatrice, che però oltre al calcolo della somma dispone anche di un metodo per restituire l'ultima somma calcolata. In questo modo ora il servizio è con stato, ovvero contiene uno stato a cui scrive (quando calcola una nuova somma) e da cui legge (quando restituisce l'ultima somma calcolata).

In Service Fabric di Azure il primo servizio è denominato servizio senza stato. Il secondo invece è denominato servizio con stato.

## Archiviazione dello stato del servizio
Lo stato può essere archiviato all'esterno oppure condividere la posizione con il codice che modifica lo stato. Nel primo caso viene usato in genere un database o un archivio esterno. Nell'esempio della calcolatrice è possibile usare un database SQL in cui il risultato corrente viene archiviato in una tabella. Ogni richiesta di calcolo della somma comporta un aggiornamento della riga corrispondente.

Lo stato può anche condividere la posizione con il codice che lo modifica. I servizi con stato in Service Fabric vengono creati usando questo modello. Service Fabric offre l'infrastruttura necessaria per garantire la disponibilità elevata e la tolleranza di errore dello stato in caso di errori.

## Passaggi successivi

Per ulteriori informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)

- [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)

- [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)

<!---HONumber=AcomDC_0810_2016-->