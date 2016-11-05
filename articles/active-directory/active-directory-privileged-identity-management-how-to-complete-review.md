---
title: Come completare una verifica dell'accesso | Microsoft Docs
description: Dopo che è stata avviata una verifica di accesso in Azure AD Privileged Identity Management, leggere le informazioni su come completare la verifica e visualizzare i risultati
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/30/2016
ms.author: kgremban

---
# Come completare una verifica dell'accesso in Azure AD Privileged Identity Management
Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo che è stata [avviata una verifica della sicurezza](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) invia automaticamente un messaggio di posta elettronica che richiede agli utenti di controllare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica, è possibile inviare le istruzioni descritte in [Come eseguire una verifica della sicurezza](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Trascorso il periodo della verifica della sicurezza o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## Gestire le verifiche della sicurezza
1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'applicazione **Azure AD Privileged Identity Management** nel dashboard.
2. Selezionare la sezione **Verifiche dell'accesso** del dashboard.
3. Fare clic sulla verifica dell'accesso che si vuole gestire.

Nel pannello dei dettagli della verifica dell'accesso sono disponibili alcune opzioni per la gestione della verifica.

![Schermata dei pulsanti di verifica dell'accesso PIM][1]

### Promemoria
Se una verifica di accesso è stata impostata in modo che gli utenti verifichino se stessi, il pulsante **Promemoria** invia una notifica.

### Arresto
Tutte le verifiche di accesso hanno una data di fine, ma il pulsante **Interrompi** consente di completare l'operazione in anticipo. Eventuali utenti non sottoposti a verifica fino a questo momento, non potranno essere controllati dopo l'interruzione della verifica. Non è possibile riavviare una verifica dopo che è stata interrotta.

### Applica
Al termine di una verifica di accesso in corrispondenza della data di fine o in caso di interruzione manuale, il pulsante **Applica** implementa il risultato della verifica. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere l'assegnazione di ruolo.

### Esporta
Per applicare manualmente i risultati della verifica della sicurezza, è possibile esportare la verifica. Il pulsante **Esporta** avvia il download di un file con estensione csv. È possibile gestire i risultati in Excel o in altri programmi in grado di aprire i file con estensione csv.

### Delete
Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la verifica dall'applicazione PIM.

> [!IMPORTANT]
> Poiché non verrà visualizzato alcun avviso prima dell'eliminazione, assicurarsi di voler eliminare la verifica.
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png

<!---HONumber=AcomDC_0706_2016-->