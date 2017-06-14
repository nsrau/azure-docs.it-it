---
title: "Non è possibile accedere alla sottoscrizione di Azure | Documentazione Microsoft"
description: Descrive come eseguire la risoluzione di alcuni problemi di accesso comuni della sottoscrizione di Azure.
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 05e23ad6bc07293f53e081b905c3a9ce1c91e6f9
ms.contentlocale: it-it
ms.lasthandoff: 04/07/2017


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Non è possibile accedere per gestire la sottoscrizione di Azure
Questo articolo illustra alcuni dei metodi più comuni per risolvere i problemi di accesso.

## <a name="page-hangs-in-the-loading-status"></a>La pagina resta in stato di caricamento
Se si blocca la pagina del browser Internet, provare ciascuna delle seguenti operazioni fino a quando non è possibile accedere al [portale di Azure](https://portal.azure.com).

* Aggiornare la pagina.
* Utilizzare un altro browser Internet.
* Se si utilizza Microsoft Internet Explorer, accedere al portale di Azure con la modalità InPrivate Browsing. 
  
  R. Fare clic sul pulsante **Strumenti** ![ ](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Sicurezza** > **InPrivate Browsing**.
  
  B. Andare al [portale di Azure](https://portal.azure.com), quindi accedere al portale.

## <a name="error-message-no-subscriptions-found"></a>Messaggio di errore "Nessuna sottoscrizione trovata"
Se l'account non dispone di autorizzazioni sufficienti, è possibile che venga visualizzato il messaggio di errore **Nessuna sottoscrizione trovata**. Assicurarsi di accedere con l'account amministratore corretto. Un amministratore account può accedere solo al [Centro account](https://account.windowsazure.com/Subscriptions). Gli amministratori del servizio (SA) e i co-amministratori (CA) hanno accesso solo al [portale di Azure](https://portal.azure.com) o al portale di Azure classico.

**Scenario 1: il messaggio di errore appare nel [portale di Azure](https://portal.azure.com)**

Per risolvere il problema:

* Verificare che sia selezionata la directory di Azure corretta facendo clic sull'account in alto a destra.

![Selezionare la directory in alto a destra nel portale di Azure](./media/billing-cannot-login-subscription/directory-switch.png)

* Se è selezionata la directory di Azure corretta ma viene comunque visualizzato l'errore, [richiedere che il proprio account venga aggiunto come proprietario](billing-add-change-azure-subscription-administrator.md).

**Scenario 2: il messaggio di errore appare nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)**

Controllare se l'account utilizzato è l'amministratore account. Per verificare chi è l'amministratore dell'account, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu Hub, selezionare **Sottoscrizione**.
3. Scegliere la sottoscrizione da controllare, quindi selezionare **Impostazioni**.
4. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Viene eseguito l'accesso automaticamente come utente diverso
Questo problema può verificarsi se si usa più di un account utente in un browser Internet.

Per risolvere il problema, utilizzare uno dei seguenti metodi alternativi:

* Svuotare la cache ed eliminare i cookie di Internet. In Internet Explorer fare clic su **Strumenti** ![pulsante Strumenti](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opzioni Internet** > **Elimina**. Verificare di aver selezionato sia le caselle di controllo per file temporanei, cookie, password e cronologia esplorazioni, quindi fare clic su Elimina.
* Riconfigurare le impostazioni di Internet Explorer per ripristinare le impostazioni personali definite. Fare clic su **Strumenti** ![pulsante strumenti](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opzioni Internet** > **Avanzate** > selezionare la casella di controllo **Elimina impostazioni personali** > **Ripristina**.
* Accedere al portale di Azure in modalità InPrivate Browsing. Fare clic sul pulsante **Strumenti** ![ ](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Sicurezza** > **InPrivate Browsing**.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) per ottenere una rapida risoluzione del problema. 


