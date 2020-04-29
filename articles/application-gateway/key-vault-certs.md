---
title: Terminazione TLS con certificati Azure Key Vault
description: Informazioni su come integrare applicazione Azure gateway con Key Vault per i certificati del server collegati ai listener abilitati per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 934cf854b0c526ed994c7dc91763f65de64fd14b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617515"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminazione TLS con certificati Key Vault

[Azure Key Vault](../key-vault/general/overview.md) è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere i segreti, le chiavi e i certificati TLS/SSL. Applicazione Azure gateway supporta l'integrazione con Key Vault per i certificati del server collegati ai listener abilitati per HTTPS. Questo supporto è limitato allo SKU V2 del gateway applicazione.

L'integrazione di Key Vault offre due modelli per la terminazione TLS:

- È possibile specificare in modo esplicito i certificati TLS/SSL collegati al listener. Questo modello è il modo tradizionale per passare i certificati TLS/SSL al gateway applicazione per la terminazione TLS.
- È possibile specificare facoltativamente un riferimento a un certificato o un segreto Key Vault esistente quando si crea un listener abilitato per HTTPS.

L'integrazione del gateway applicazione con Key Vault offre molti vantaggi, tra cui:

- Maggiore sicurezza, perché i certificati TLS/SSL non sono gestiti direttamente dal team di sviluppo di applicazioni. L'integrazione consente a un team di sicurezza separato di:
  * Configurare i gateway applicazione.
  * Controllare i cicli di vita del gateway applicazione.
  * Concedere le autorizzazioni ai gateway applicazione selezionati per accedere ai certificati archiviati nell'insieme di credenziali delle chiavi.
- Supporto per l'importazione di certificati esistenti nell'insieme di credenziali delle chiavi. In alternativa, è possibile usare Key Vault API per creare e gestire nuovi certificati con i partner Key Vault attendibili.
- Supporto per il rinnovo automatico dei certificati archiviati nell'insieme di credenziali delle chiavi.

Il gateway applicazione supporta attualmente solo certificati convalidati per il software. Modulo di protezione hardware (HSM): i certificati convalidati non sono supportati. Dopo la configurazione del gateway applicazione per l'uso di certificati Key Vault, le relative istanze recuperano il certificato da Key Vault e le installano localmente per la terminazione TLS. Anche le istanze eseguono il polling Key Vault a intervalli di 24 ore per recuperare una versione rinnovata del certificato, se esistente. Se viene trovato un certificato aggiornato, il certificato TLS/SSL attualmente associato al listener HTTPS viene ruotato automaticamente.

> [!NOTE]
> Il portale di Azure supporta solo certificati dell'insieme di credenziali delle chiavi, non segreti. Il gateway applicazione supporta ancora i segreti di riferimento da un insieme di credenziali delle chiavi, ma solo tramite risorse non del portale come PowerShell, CLI, API, modelli ARM e così via. 

## <a name="how-integration-works"></a>Funzionamento dell'integrazione

L'integrazione del gateway applicazione con Key Vault richiede un processo di configurazione in tre passaggi:

1. **Creare un'identità gestita assegnata dall'utente**

   Si crea o si riutilizza un'identità gestita assegnata dall'utente esistente, che il gateway applicazione usa per recuperare i certificati da Key Vault per conto dell'utente. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Questo passaggio consente di creare una nuova identità nel tenant Azure Active Directory. L'identità è considerata attendibile dalla sottoscrizione usata per creare l'identità.

1. **Configurare l'insieme di credenziali delle chiavi**

   È quindi possibile importare un certificato esistente o crearne uno nuovo nell'insieme di credenziali delle chiavi. Il certificato verrà usato dalle applicazioni che vengono eseguite tramite il gateway applicazione. In questo passaggio, è anche possibile usare un segreto dell'insieme di credenziali delle chiavi archiviato come file PFX con codifica base-64 senza password. È consigliabile usare un tipo di certificato a causa della funzionalità di rinnovo automatico disponibile con gli oggetti tipo di certificato nell'insieme di credenziali delle chiavi. Dopo aver creato un certificato o un segreto, è necessario definire i criteri di accesso nell'insieme di credenziali delle chiavi per consentire all'identità di concedere *l'accesso al* segreto.
   
   > [!NOTE]
   > Se si distribuisce il gateway applicazione tramite un modello ARM, usando l'interfaccia della riga di comando di Azure o PowerShell oppure tramite un applicazione Azure distribuito dal portale di Azure, il certificato SSL archiviato nell'insieme di credenziali delle chiavi come file PFX codificato in base 64 **deve essere senza password**. Inoltre, è necessario completare i passaggi descritti in [usare Azure Key Vault per passare il valore del parametro Secure durante la distribuzione](../azure-resource-manager/templates/key-vault-parameter.md). È particolarmente importante impostare `enabledForTemplateDeployment` su. `true`

1. **Configurare il gateway applicazione**

   Dopo aver completato i due passaggi precedenti, è possibile impostare o modificare un gateway applicazione esistente per usare l'identità gestita assegnata dall'utente. È anche possibile configurare il certificato TLS/SSL del listener HTTP in modo che punti all'URI completo del certificato Key Vault o dell'ID segreto.

   ![Certificati di Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare la terminazione TLS con Key Vault certificati usando Azure PowerShell](configure-keyvault-ps.md)
