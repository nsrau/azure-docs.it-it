---
title: Informazioni sul rinnovo dei certificati di Azure Key Vault
description: Questo articolo illustra come rinnovare i certificati di Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 0720e6b55cec8150eea9d41ca89b2c9b21a0bc94
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287682"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Rinnovare i certificati di Azure Key Vault

Azure Key Vault consente di effettuare facilmente il provisioning, la gestione e la distribuzione di certificati digitali per la rete e di abilitare comunicazioni sicure per le applicazioni. Per altre informazioni sui certificati, vedere [Informazioni sui certificati di Azure Key Vault](./about-certificates.md).

Usando certificati di breve durata o incrementando la frequenza della rotazione dei certificati, è possibile contribuire a evitare l'accesso alle applicazioni da parte di utenti non autorizzati.

Questo articolo illustra come rinnovare i certificati di Azure Key Vault.

## <a name="get-notified-about-certificate-expiration"></a>Ricevere notifiche sulle scadenze dei certificati
Per ricevere notifiche sugli eventi di durata del certificato, è necessario aggiungere il contatto del certificato. I contatti relativi al certificato contengono le informazioni di contatto per inviare notifiche attivate da eventi di durata dei certificati. Le informazioni dei contatti vengono condivise da tutti i certificati nell'insieme di credenziali delle chiavi. Viene inviata una notifica a tutti i contatti specificati per un evento per qualsiasi certificato presente nell'insieme di credenziali delle chiavi.

### <a name="steps-to-set-certificate-notifications"></a>Passaggi per impostare le notifiche sui certificati:
Aggiungere prima di tutto un contatto del certificato all'insieme di credenziali delle chiavi. A questo scopo è possibile usare il portale di Azure o il cmdlet [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0) di PowerShell.

Configurare quindi quando si vogliono ricevere notifiche sulla scadenza dei certificati. Per configurare gli attributi del ciclo di vita del certificato, vedere [Configurare la rotazione automatica dei certificati in Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Se i criteri del certificato sono impostati per il rinnovo automatico, viene inviata una notifica sugli eventi seguenti.

- Prima del rinnovo del certificato
- Dopo il rinnovo del certificato, che indica se il certificato è stato rinnovato, o se si è verificato un errore, richiede il rinnovo manuale del certificato.  

  Se i criteri del certificato sono impostati per il rinnovo manuale (solo posta elettronica), viene inviata una notifica al momento del rinnovo.  

In Key Vault sono disponibili tre categorie di certificati:
-   Certificati creati con un'autorità di certificazione integrata, ad esempio DigiCert o GlobalSign
-   Certificati creati con un'autorità di certificazione non integrata
-   Certificati autofirmati

## <a name="renew-an-integrated-ca-certificate"></a>Rinnovare un certificato di un'autorità di certificazione integrata 
Azure Key Vault gestisce la manutenzione end-to-end dei certificati rilasciati da autorità di certificazione Microsoft attendibili, ovvero DigiCert e GlobalSign. Informazioni su come [integrare un'autorità di certificazione con Key Vault](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Rinnovare un certificato di un'autorità di certificazione non integrata 
Azure Key Vault consente di importare certificati da un'autorità di certificazione. Questo vantaggio permette l'integrazione con alcune risorse di Azure e semplifica la distribuzione. Se si teme di dimenticare le date di scadenza dei certificati o si è addirittura scoperto che un certificato è già scaduto, l'insieme di credenziali delle chiavi può fornire informazioni sempre aggiornate. Per i certificati di autorità di certificazione non integrate, l'insieme di credenziali delle chiavi consente di configurare notifiche tramite posta elettronica all'avvicinarsi della scadenza. Queste notifiche possono essere configurate anche per più utenti.

> [!IMPORTANT]
> Un certificato è un oggetto con versione. Se la versione corrente sta per scadere, è necessario creare una nuova versione. Ogni nuova versione è concettualmente un nuovo certificato costituito da una chiave e da un BLOB che associa la chiave a un'identità. Quando si usa un'autorità di certificazione non partner, l'insieme di credenziali delle chiavi genera una coppia chiave/valore e restituisce una richiesta di firma del certificato.

Per rinnovare un certificato di un'autorità di certificazione non integrata, seguire questa procedura:

1. Accedere al portale di Azure e quindi aprire il certificato da rinnovare.
1. Nel riquadro del certificato selezionare **Nuova versione**.
1. Selezionare **Operazione relativa al certificato**.
1. Selezionare **Scarica file CSR** per scaricare un file CSR nell'unità locale.
1. Inviare il file CSR all'autorità di certificazione preferita per firmare la richiesta.
1. Visualizzare di nuovo la richiesta firmata e selezionare **Merge CSR** (Unisci CSR) nel riquadro delle operazioni dello stesso certificato.

> [!NOTE]
> È importante unire la richiesta di firma del certificato firmata con la stessa richiesta di firma del certificato creata. In caso contrario, non corrisponderanno.

Per altre informazioni sulla creazione di una nuova richiesta di firma del certificato, vedere [Creare e unire una richiesta di firma del certificato in Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Rinnovare un certificato autofirmato

Azure Key Vault gestisce anche il rinnovo automatico dei certificati autofirmati. Per altre informazioni sulla modifica dei criteri di rilascio e sull'aggiornamento degli attributi del ciclo di vita di un certificato, vedere [Configurare la rotazione automatica dei certificati in Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Risolvere problemi
Se lo stato del certificato rilasciato è *Disabilitato* nel portale di Azure, passare a **Operazione relativa al certificato** per visualizzare il messaggio di errore del certificato.

## <a name="frequently-asked-questions"></a>Domande frequenti

**Come si può testare la funzionalità di rotazione automatica del certificato?**

Creare un certificato con validità pari a **1 mese** e quindi impostare l'azione della durata per la rotazione su **1%** . Questa impostazione ruoterà il certificato ogni 7,2 ore.
  
**I tag verranno replicati dopo il rinnovo automatico del certificato?**

Sì, i tag vengono replicati dopo il rinnovo automatico.

## <a name="next-steps"></a>Passaggi successivi
*   [Integrare Key Vault con l'autorità di certificazione DigiCert](how-to-integrate-certificate-authority.md)
*   [Esercitazione: Configurare la rotazione automatica dei certificati in Key Vault](tutorial-rotate-certificates.md)