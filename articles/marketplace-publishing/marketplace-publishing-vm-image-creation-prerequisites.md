<properties
   pageTitle="Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace | Microsoft Azure"
   description="Informazioni sui requisiti per la creazione e la distribuzione di un'immagine di macchina virtuale in Azure Marketplace per l'acquisto da parte di altri utenti."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="02/04/2016"
  ms.author="hascipio; v-divte"/>

# Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace
Leggere attentamente le informazioni sul processo prima di iniziare e comprendere dove e perché viene eseguito ogni passaggio. Per quanto possibile, è necessario preparare le informazioni sulla società e altri dati, scaricare gli strumenti necessari e/o creare i componenti tecnici prima di iniziare il processo di creazione dell'offerta. Questi elementi dovrebbero risultare chiari dalla lettura di questo articolo.

## Scaricare gli strumenti e le applicazioni necessari
È necessario disporre dei seguenti elementi prima di iniziare il processo:

- A seconda del sistema operativo di destinazione, installare i cmdlet di Azure PowerShell o lo strumento di interfaccia della riga di comando di Linux dalla pagina dei download di Azure.
- Installare lo strumento di esplorazione dell'archiviazione di Azure da CodePlex.
- Scaricare e installare lo strumento di test della certificazione per Azure Certified:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). È necessario un computer basato su Windows per eseguire lo strumento di certificazione. Se non è disponibile un computer basato su Windows, è possibile eseguire lo strumento usando una macchina virtuale basata su Windows in Azure.

## Piattaforme supportate
È possibile sviluppare macchine virtuali basate su Azure in Windows o Linux. Per alcuni elementi del processo di pubblicazione, ad esempio la creazione di un disco rigido virtuale (VHD) compatibile con Azure, vengono usati strumenti e passaggi diversi a seconda del sistema operativo in uso:

- Se si usa Linux, vedere la sezione relativa alla creazione di un disco rigido virtuale (basato su Linux) compatibile con Azure della [Guida alla pubblicazione di un'immagine di macchina virtuale](marketplace-publishing-vm-image-creation.md).
- Se si usa Windows, vedere la sezione relativa alla creazione di un disco rigido virtuale (basato su Windows) compatibile con Azure della [Guida alla pubblicazione di un'immagine di macchina virtuale](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Sarà necessario accedere a un computer Windows per: - Eseguire lo strumento di convalida della certificazione - Creare l'URL della firma di accesso condiviso del disco rigido virtuale per l'invio della certificazione del disco rigido virtuale.

## Sviluppare il disco rigido virtuale
È possibile sviluppare i dischi rigidi virtuali di Azure nel cloud o in locale:

- Lo sviluppo basato sul cloud implica che tutte le operazioni di sviluppo vengono eseguite in modalità remota su un disco rigido virtuale residente in Azure.
- Lo sviluppo in locale richiede il download di un disco rigido virtuale e il relativo sviluppo tramite l'infrastruttura locale. Sebbene questa operazione sia possibile, non è consigliabile. Si noti che lo sviluppo per Windows o SQL in locale richiede la disponibilità in locale dei codici di licenza appropriati. Non è possibile includere o installare SQL Server dopo aver creato una VM. È inoltre necessario basare l’offerta su un'immagine SQL approvata dal portale di Azure. Se si decide di eseguire lo sviluppo in locale, è necessario effettuare alcuni passaggi in modo diverso rispetto allo sviluppo nel cloud. È possibile trovare le informazioni appropriate in [Creare un'immagine di VM in locale](marketplace-publishing-vm-image-creation-on-premise.md).

## Passaggi successivi
Dopo avere esaminato i prerequisiti e completato le attività necessarie, ora è possibile procedere alla creazione dell'offerta di un'immagine di macchina virtuale, come illustrato in dettaglio nella [Guida alla pubblicazione di un'immagine di macchina virtuale](marketplace-publishing-vm-image-creation.md)

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines/virtual-machines-windows-tutorial/)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=AcomDC_0211_2016-->