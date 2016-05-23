<properties
   pageTitle="Accesso alle macchine virtuali di Azure da Esplora server | Microsoft Azure"
   description="Panoramica su come visualizzare, creare e gestire macchine virtuali di Azure (VM) in Esplora Server in Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# Accesso alle macchine virtuali di Azure da Esplora server

Con Esplora server in Visual Studio è possibile visualizzare informazioni sulle macchine virtuali ospitate da Azure.

## Accesso alle macchine virtuali in Esplora server

Se ci sono macchine virtuali ospitate da Azure, è possibile accedervi in Esplora server. Per visualizzare i servizi mobili, è necessario prima di tutto accedere alla sottoscrizione Azure. Per accedere, aprire il menu di scelta rapida per il nodo Azure e scegliere **Connetti a Microsoft Azure**.

### Per ottenere informazioni sulle macchine virtuali

1. In Esplora server scegliere una macchina virtuale, quindi premere F4 per visualizzare la finestra delle proprietà.

    La tabella seguente illustra le proprietà disponibili, le quali sono tuttavia di sola lettura. Per modificarle, usare il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Proprietà|Descrizione|
  	|---|---|
  	|Nome DNS|URL con l'indirizzo Internet della macchina virtuale.|
  	|Environment|Per una macchina virtuale, il valore di questa proprietà è sempre Produzione.|
  	|Nome|Nome della macchina virtuale.|
  	|Dimensione|Dimensioni della macchina virtuale corrispondenti alla quantità di memoria e spazio su disco disponibili. Per altre informazioni, vedere Come configurare le dimensioni della macchina virtuale.|
  	|Stato|I valori includono Avvio in corso, Avviato, Arresto, Arrestato e Recupero dello stato. Se viene visualizzato Recupero dello stato, lo stato corrente è sconosciuto. I valori di questa proprietà differiscono da quelli usati nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|ID sottoscrizione dell'account Azure. È possibile vedere queste informazioni nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885) visualizzando le proprietà di una sottoscrizione.|

1. Scegliere un nodo di endpoint e quindi visualizzare la finestra **Proprietà**.

1. La tabella seguente descrive le proprietà disponibili degli endpoint, che però sono di sola lettura. Per aggiungere o modificare gli endpoint di una macchina virtuale, usare il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Proprietà|Descrizione|
  	|---|---|
  	|Nome|Identificatore dell'endpoint.|
  	|Private Port|Porta per l'accesso di rete interno all'applicazione.|
  	|Protocollo|Protocollo usato dal livello di trasporto per l'endpoint TCP o UDP.|
  	|Public Port|Porta usata per l'accesso pubblico all'applicazione.|

## Passaggi successivi

Per ulteriori informazioni sull'utilizzo dei ruoli di Azure in Visual Studio, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md).

<!---HONumber=AcomDC_0511_2016-->