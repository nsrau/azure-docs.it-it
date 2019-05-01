---
title: Terminazione SSL con certificati Key Vault
description: Informazioni su come è possibile integrare il gateway applicazione di Azure Key Vault per i certificati server collegati di listener di traccia abilitato per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 37707d56caabf0ae8b0020eb8714245a27501ea6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696504"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminazione SSL con certificati Key Vault

[Azure Key Vault](../key-vault/key-vault-whatis.md) è un archivio segreti gestiti piattaforma è possibile usare per proteggere i segreti, chiavi e certificati SSL. Il Gateway applicazione supporta l'integrazione con Key Vault (in anteprima pubblica) per i certificati server collegati di listener di traccia abilitato per HTTPS. Questo supporto è limitato a v2 SKU del Gateway applicazione.

> [!IMPORTANT]
> L'integrazione dell'applicazione Gateway Key Vault è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Esistono due modelli per la terminazione SSL con questa versione di anteprima pubblica:

- È possibile fornire in modo esplicito i certificati SSL collegati al listener. Si tratta del modello tradizionale di passare i certificati SSL per Gateway applicazione per la terminazione SSL.
- È possibile fornire facoltativamente un riferimento a un certificato di Key Vault esistente o segreto durante HTTPS è abilitata la creazione del listener.

Esistono diversi vantaggi con l'integrazione di Key Vault, tra cui:

- Una maggiore sicurezza perché i certificati SSL non sono gestiti direttamente dal team di sviluppo dell'applicazione. Integrazione con Key Vault consente a un team di sicurezza separati effettuare il provisioning di controllo del ciclo di vita e concedere l'autorizzazione appropriata per selezionare il gateway applicazione per accedere ai certificati archiviati in Azure Key Vault.
- Supporto per importare i certificati esistenti in Key Vault o usare le API dell'insieme di credenziali chiave per creare e gestire nuovi certificati con uno qualsiasi dei partner di Key Vault attendibile.
- Supporto per i certificati archiviati in Azure Key Vault per rinnovare automaticamente.

Il Gateway applicazione supporta attualmente solo i certificati software convalidato. Hardware security module (HSM) convalidati certificati non sono supportati. Dopo aver configurato il Gateway applicazione per usare i certificati di Key Vault, le relative istanze recupero il certificato di Key Vault e installarli localmente per la terminazione SSL. Le istanze anche eseguire periodicamente il polling dell'insieme di credenziali chiave in base a un intervallo di 24 ore per recuperare una versione di rinnovata del certificato, se presente. Se viene trovato un certificato aggiornato, il certificato SSL attualmente associato al Listener HTTPS automaticamente viene ruotato.

## <a name="how-it-works"></a>Funzionamento

Integrazione con Key Vault richiede un processo di configurazione in tre passaggi:

1. **Creare gestito identità assegnata all'utente**

   È necessario creare o riusare un utente esistente assegnato identità gestita che usa il Gateway applicazione per recuperare i certificati da Key Vault per tuo conto. Per altre informazioni, vedere [What ' s identità gestita per le risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md) Questo passaggio Crea una nuova identità nel tenant di Azure AD, che è considerato attendibile dalla sottoscrizione usata per creare l'identità.
1. **Configurare l'insieme di credenziali delle chiavi**

   Quindi necessario importare o creare un nuovo certificato in Key Vault usato dalle applicazioni in esecuzione tramite il Gateway applicazione. Un segreto di Key Vault archiviato come password senza base 64 codificata file PFX può essere usato anche in questo passaggio. Utilizzo di un tipo di certificato è preferibile perché le funzionalità di rinnovo automatico disponibili con gli oggetti di tipo certificato in Key Vault. Dopo aver creato un certificato o chiave privata, è necessario definire i criteri di accesso in Key Vault per consentire l'identità a cui concedere *ottenere* accesso per recuperare il segreto.

1. **Configurare il Gateway applicazione**

   Dopo aver completati i due passaggi precedenti, è possibile eseguire il provisioning o modificare un Gateway applicazione esistente per usare l'identità gestita assegnata all'utente. È inoltre configurare il certificato SSL del listener HTTP in modo da puntare al certificato o segreto ID. il completo URI della chiave dell'insieme di credenziali

![Certificati Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passaggi successivi

[Configura la terminazione SSL con certificati Key Vault con Azure PowerShell](configure-keyvault-ps.md).