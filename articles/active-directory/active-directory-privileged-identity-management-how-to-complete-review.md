<properties
   pageTitle="Come completare una verifica della sicurezza | Microsoft Azure"
   description="Informazioni su come completare una verifica con l'applicazione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Come completare una verifica della sicurezza in Azure AD Privileged Identity Management


Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo che è stata [avviata una verifica della sicurezza](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) invia automaticamente un messaggio di posta elettronica che richiede agli utenti di controllare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica, è possibile inviare le istruzioni descritte in [Come eseguire una verifica della sicurezza](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Trascorso il periodo della verifica della sicurezza o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## Gestire le verifiche della sicurezza

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'applicazione **Azure AD Privileged Identity Management** nel dashboard.
2. Selezionare la sezione **Verifiche della sicurezza** del dashboard. Verrà visualizzato il pannello **Revisioni sicurezza**.
3. Fare clic sulla verifica della sicurezza che si vuole gestire. Verrà visualizzato il pannello con i dettagli della verifica della sicurezza.

Nel pannello dei dettagli della verifica della sicurezza sono disponibili tre opzioni per la gestione della verifica. È possibile completare la verifica, esportare i dettagli o eliminarla.

### Completare o interrompere una verifica

Se agli utenti viene negato l'accesso, è possibile completare la verifica per rimuovere le assegnazioni dei ruoli degli utenti corrispondenti nella directory. Il pulsante **Interrompi verifica** archivierà la verifica.

### Esportare una verifica

Per applicare manualmente i risultati della verifica della sicurezza, è possibile esportare la verifica. Il pulsante **Esporta** avvia il download di un file con estensione csv. È possibile gestire i risultati in Excel o in altri programmi in grado di aprire i file con estensione csv.

### Eliminare una verifica

> [AZURE.IMPORTANT] Poiché non verrà visualizzato alcun avviso prima dell'eliminazione, assicurarsi di voler eliminare la verifica.

Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la verifica dall'applicazione PIM.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->