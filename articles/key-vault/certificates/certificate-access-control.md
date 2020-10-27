---
title: Informazioni sul controllo di accesso per i certificati di Azure Key Vault
description: Panoramica del controllo di accesso per i certificati di Azure Key Vault
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128575"
---
# <a name="certificate-access-control"></a>Controllo di accesso per i certificati

 Il controllo di accesso per i certificati è gestito da Key Vault e viene fornito dall'insieme di credenziali delle chiavi che contiene tali certificati. I criteri di controllo di accesso per i certificati sono distinti dai criteri di controllo di accesso per le chiavi e i segreti presenti nello stesso insieme di credenziali delle chiavi. Gli utenti possono creare uno o più insiemi di credenziali per i certificati e per mantenere una segmentazione e una gestione dei certificati appropriate in base allo scenario.  

 Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali delle chiavi e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

- Autorizzazioni per le operazioni di gestione dei certificati
  - **get** : consente di ottenere la versione corrente del certificato o qualsiasi versione di un certificato
  - **list** : consente di elencare i certificati correnti o le versioni di un certificato  
  - **update** : consente di aggiornare un certificato
  - **create** : consente di creare un certificato di Key Vault
  - **import** : consente di importare il materiale apposito in un certificato di Key Vault
  - **delete** : consente di eliminare un certificato, nonché i relativi criteri e versioni  
  - **recover** : consente di recuperare un certificato eliminato
  - **backup** : consente di eseguire il backup di un certificato nell'insieme di credenziali delle chiavi
  - **restore** : consente di ripristinare un certificato sottoposto a backup in un insieme di credenziali delle chiavi
  - **managecontacts** : consente di gestire i contatti del certificato in un Key Vault  
  - **manageissuers** : consente di gestire autorità di certificazione di Key Vault
  - **getissuers** : consente di ottenere le autorità emittenti del certificato
  - **listissuers** : consente di elencare le autorità emittenti del certificato  
  - **setissuers** : consente di creare o aggiornare le autorità di certificazione di un certificato di Key Vault  
  - **deleteissuers** : consente di eliminare le autorità di certificazione del certificato di Key Vault  
 
- Autorizzazioni per le operazioni privilegiate
  - **purge** : consente di eliminare definitivamente un certificato

Per altre informazioni, vedere le [operazioni relative ai certificati nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione della autorizzazioni, vedere [Insiemi di credenziali: aggiornare i criteri di accesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Risolvere problemi
È possibile che venga visualizzato un errore a causa di criteri di accesso mancanti, ad esempio ```Error type : Access denied or user is unauthorized to create certificate```. Per correggere l'errore, è necessario aggiungere i certificati o creare l'autorizzazione.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Key Vault](../general/overview.md)
- [Informazioni su chiavi, segreti e certificati](../general/about-keys-secrets-certificates.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
