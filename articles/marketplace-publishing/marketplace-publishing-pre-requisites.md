---
title: Prerequisiti non tecnici per la creazione di un'offerta per Azure Marketplace | Microsoft Docs
description: Informazioni sui requisiti per la creazione e la distribuzione di un'offerta in Azure Marketplace per l'acquisto da parte di altri utenti.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: ''
editor: ''

ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio

---
# Prerequisiti generali per la creazione di un'offerta per Azure Marketplace
Informazioni sui prerequisiti generali e relativi ai processi aziendali necessari per procedere con il processo di creazione dell'offerta.

## Assicurarsi di essere registrati come venditore con Microsoft
Per istruzioni dettagliate su come registrare un account venditore con Microsoft, visitare [Creazione e registrazione dell'account](marketplace-publishing-accounts-creation-registration.md).

* **Se l'azienda è già registrata come venditore in Dev Center e si desidera creare una nuova offerta**, eseguire l'accesso al portale di pubblicazione con lo stesso ID di posta elettronica con cui si è eseguita la registrazione in Dev Center. Questo passaggio è necessario affinché Dev Center e il portale di pubblicazione siano collegati tra loro.
* **Se l'azienda è già registrata come venditore Dev Center e si desidera modificare un'offerta esistente**, effettuare l'accesso al portale di pubblicazione con l'account di amministratore o con un account che viene aggiunto come coamministratore nel portale di pubblicazione. I passaggi per aggiungere un account di coamministratore sono riportati di seguito.

## Passaggi per aggiungere un coamministratore nel portale di pubblicazione
Gli amministratori del portale di pubblicazione possono aggiungere in un secondo momento altri membri dell'azienda che lavorano all'applicazione, ad esempio un coamministratore, nel portale di pubblicazione. **Come amministratore**, di seguito sono riportati i passaggi per aggiungere un coamministratore.

> [!NOTE]
> Per i nuovi utenti, prima di aggiungere un coamministratore nel portale di pubblicazione, assicurarsi di avere creato almeno un'applicazione nel portale di pubblicazione. Questa operazione è necessaria in quanto la scheda **AUTORI** viene visualizzata solo dopo avere creato almeno un'applicazione nel portale di pubblicazione.
> 
> 

1. Assicurarsi che l'ID di posta elettronica del coamministratore sia un account Microsoft (MSA). In caso contrario, registrarlo come MSA mediante questo [collegamento](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Verificare che sia presente almeno un'applicazione nell'account amministratore prima di tentare di aggiungere un coamministratore.
3. Dopo aver completato i passaggi precedenti, eseguire l'accesso al portale di pubblicazione con l'ID di posta elettronica di coamministratore e quindi eseguire la disconnessione.
4. Ora accedere al portale di pubblicazione con l'ID di posta elettronica di amministratore.
5. Passare ad Autore -> selezionare l'account-> Amministratori -> Aggiungi il coamministratore (la schermata è riportata di seguito)
   
    ![disegno](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Assicurarsi che gli ID di posta elettronica forniti nelle varie fasi del processo di pubblicazione (ad esempio Dev Center, portale di pubblicazione) vengano monitorati per le comunicazioni provenienti da Microsoft.
7. Per la registrazione in Dev Center, evitare di usare un account associato a una singola persona. Si consiglia di procedere in questo modo per eliminare la dipendenza da una singola persona.
8. In caso di problemi durante la registrazione in Dev Center, creare un ticket mediante questo [collegamento](https://developer.microsoft.com/it-IT/windows/support).

## Passaggi per eliminare un coamministratore nel portale di pubblicazione
**Come amministratore**, di seguito sono riportati i passaggi per eliminare un coamministratore.

1. Accedere al portale di pubblicazione con l'ID di posta elettronica di amministratore.
2. Passare ad **Autori** -> selezionare il proprio account -> **Amministratori** -> **Coamministratori**.
3. Fare sulla **X** accanto al co-amministratore da eliminare (schermata riportata di seguito).
   
    ![disegno](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Non è necessario inserire le informazioni fiscali e bancarie dell'azienda se si intende pubblicare solo offerte gratuite (o Bring Your Own License).
> 
> Per iniziare, è necessario completare la registrazione dell'azienda. Tuttavia, mentre la società si occupa delle informazioni fiscali e bancarie nell'account Dev Center, gli sviluppatori possono iniziare a lavorare alla creazione dell'immagine di macchina virtuale nel [portale di pubblicazione](https://publish.windowsazure.com), ottenere la certificazione ed eseguire il testing nell'ambiente di gestione temporanea di Azure. L'approvazione completa dell'account venditore è necessaria solo per il passaggio finale di pubblicazione dell'offerta in Azure Marketplace.
> 
> 

## Acquisire una sottoscrizione di Azure con pagamento in base al consumo
Si tratta della sottoscrizione che si userà per creare le immagini di VM e distribuire le immagini in [Azure Marketplace](https://azure.microsoft.com/marketplace/). Se non si dispone di una sottoscrizione esistente, iscriversi a https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## Paesi di origine della vendita
> [!WARNING]
> Per vendere i propri servizi in Microsoft Azure Marketplace, è necessario assicurarsi che l'entità registrata si trovi in uno dei paesi di origine della vendita approvati. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Questo elenco di paesi verrà ampliato nel prossimo futuro perciò non rimane che attendere. Per l'elenco completo, vedere la sezione 1b delle [politiche di partecipazione ad Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## Passaggi successivi
Quando vengono rispettati i prerequisiti non tecnici, occorre passare ai prerequisiti tecnici specifici dell'offerta. Fare clic sul collegamento all'articolo per il tipo di offerta corrispondente che si desidera creare per Azure Marketplace.

* [Prerequisiti tecnici per le macchine virtuali](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Prerequisiti tecnici per il modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md)

## Vedere anche
* [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0824_2016-->