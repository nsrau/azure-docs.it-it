---
title: Risoluzione dei problemi relativi ai criteri di accesso di Azure Key Vault
description: Risoluzione dei problemi relativi ai criteri di accesso di Azure Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: f775a0e1dcb82afc9a458ce4ee608a52ec06dc4c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135913"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Risoluzione dei problemi relativi ai criteri di accesso di Azure Key Vault

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>In che modo è possibile identificare le tempistiche e le modalità di accesso agli insiemi di credenziali delle chiavi?
Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. A tale scopo, è necessario abilitare la registrazione per Azure Key Vault. Per una guida dettagliata per abilitare la registrazione, [leggere altre informazioni](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Come è possibile monitorare la disponibilità, i periodi di latenza del servizio o altre metriche delle prestazioni per l'insieme di credenziali?
Quando si inizia a ridimensionare il servizio, il numero di richieste inviate all'insieme di credenziali delle chiavi aumenta. Di conseguenza, può aumentare anche la latenza delle richieste e, in casi estremi, le richieste possono venire limitate, con un impatto sulle prestazioni del servizio. È possibile monitorare le metriche delle prestazioni dell'insieme di credenziali delle chiavi e ricevere avvisi per soglie specifiche. Per una guida dettagliata per configurare il monitoraggio, [leggere altre informazioni](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>In che modo è possibile assegnare il controllo di accesso per ogni oggetto dell'insieme di credenziali delle chiavi? 
La disponibilità della funzionalità di controllo di accesso per ogni segreto, chiave o certificato verrà segnalata qui. [Altre informazioni](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>In che modo è possibile fornire l'autenticazione dell'insieme di credenziali delle chiavi usando criteri di controllo di accesso?
Il modo più semplice per autenticare un'applicazione basata sul cloud in Key Vault consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Usare un'identità gestita del servizio app per accedere ad Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity).
Se si crea un'applicazione locale, si sviluppa in locale o se non è possibile usare un'identità gestita per altri motivi, è possibile registrare un'entità servizio manualmente e fornire l'accesso all'insieme di credenziali delle chiavi usando i criteri di controllo di accesso. [Altre informazioni](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Come è possibile concedere al gruppo di AD l'accesso all'insieme di credenziali delle chiavi?
Concedere al gruppo di AD le autorizzazioni per l'insieme di credenziali delle chiavi usando il comando az keyvault set-policy dell'interfaccia della riga di comando di Azure o il cmdlet Set-AzKeyVaultAccessPolicy di Azure PowerShell. Per gli esempi, vedere [Concedere all'applicazione, al gruppo di Azure AD o all'utente l'accesso all'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

L'applicazione necessita anche che sia assegnato almeno un ruolo di gestione delle identità e degli accessi (IAM) all'insieme di credenziali delle chiavi. In caso contrario, non potrà eseguire l'accesso e l'accesso alla sottoscrizione non riuscirà a causa di diritti insufficienti. I gruppi di Azure AD con le identità gestite possono richiedere fino a 8 ore per aggiornare il token e diventare effettivi.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>In che modo è possibile ridistribuire Key Vault con un modello di Azure Resource Manager senza eliminare i criteri di accesso esistenti?
Attualmente, la ridistribuzione di Key Vault con Azure Resource Manager comporta l'eliminazione dei criteri di accesso in Key Vault, che vengono sostituiti con quelli nel modello di Azure Resource Manager. Non è disponibile alcuna opzione incrementale per i criteri di accesso di Key Vault. Per mantenere i criteri di accesso in Key Vault, è necessario leggere i criteri di accesso esistenti in Key Vault e popolare il modello di Azure Resource Manager con tali criteri per evitare interruzioni di accesso.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Procedure consigliate per la risoluzione dei problemi per i tipi di errore seguenti
* HTTP 401: Richiesta non autenticata - [Procedura di risoluzione dei problemi](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: Autorizzazioni insufficienti - [Procedura di risoluzione dei problemi](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: Troppe richieste - [Procedura di risoluzione dei problemi](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Controllare se è stata eliminata l'autorizzazione di accesso all’insieme di credenziali delle chiavi: [Criteri di accesso di Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Se si riscontra un problema con l'autenticazione nell'insieme di credenziali delle chiavi nel codice, usare l'[SDK di autenticazione](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Quali sono le procedure consigliate da implementare in caso di limitazione dell'insieme di credenziali delle chiavi?
Seguire le procedure consigliate illustrate [qui](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come risolvere gli errori di autenticazione di Key Vault. [Guida alla risoluzione dei problemi di Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
