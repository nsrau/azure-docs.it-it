---
title: Informazioni sul rinnovo del certificato Azure Key Vault
description: Informazioni sul rinnovo del certificato Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: c6999b67a5c0a0f4ca7cb943ae8de3afd8b6a11e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098277"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Informazioni sul rinnovo del certificato Azure Key Vault

Azure Key Vault consente di effettuare facilmente il provisioning, la gestione e la distribuzione di certificati digitali per la rete e di abilitare comunicazioni sicure per le applicazioni. Per informazioni più generali sui certificati, vedere [Azure Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates) .

La presenza di un certificato di breve durata o l'aumento della frequenza della rotazione del certificato limita l'ambito dell'avversario per danni.

Sono presenti tre categorie di creazione di certificati in Key Vault. Questa guida consente di comprendere il modo in cui è possibile ottenere il rinnovo dei certificati.
-   Certificati creati con CA integrata (DigiCert o GlobalSign)
-   Certificati creati con una CA non integrata
-   Certificati autofirmati

## <a name="renewal-of-integrated-ca-certificate"></a>Rinnovo del certificato CA integrato 
Ottime notizie! Azure Key Vault gestisce la manutenzione end-to-end dei certificati rilasciati da CA attendibili Microsoft, ad esempio DigiCert e GlobalSign. Informazioni su come [integrare una CA attendibile con Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Rinnovo del certificato CA non integrato 
Azure Key Vault offre agli utenti il vantaggio di importare i certificati da qualsiasi CA per consentire agli utenti di integrarsi con diverse risorse di Azure e semplificare la distribuzione. Se si è preoccupati di perdere la traccia del certificato per la scadenza, oppure è stato rilevato che il certificato è già scaduto; quindi Key Vault possibile rimanere aggiornati. Per il certificato della CA non integrato, Key Vault consente all'utente di configurare le notifiche di posta elettronica prossime alla scadenza. Queste notifiche possono essere impostate anche per più utenti.

A questo punto, per rinnovare un certificato, è importante comprendere che un certificato di Azure Key Vault è un oggetto con versione. Se la versione corrente sta per scadere, è necessario creare una nuova versione. Concettualmente, ogni nuova versione sarebbe completamente un nuovo certificato costituito da chiave e da un BLOB che associa tale chiave a un'identità. Quando si usa una CA non partner, Key Vault genererà una coppia chiave-valore e restituirà la richiesta CSR.

**Passaggi da seguire in portale di Azure:-**
1.  Aprire il certificato da rinnovare.
2.  Selezionare **+ nuovo pulsante versione** nella schermata del certificato.
3.  Seleziona **operazione certificato**
4.  Selezionare **Scarica CSR**. Verrà scaricato un file con estensione CSR nell'unità locale.
5.  Porta CSR alla scelta della CA per firmare la richiesta
6.  Ripristinare la richiesta firmata e selezionare **merge CSR** nella stessa schermata operazione del certificato.

> [!NOTE]
> È importante eseguire il merge del CSR firmato con la stessa richiesta CSR che è stata creata; in caso contrario, la chiave non corrisponde.

I passaggi sono simili alla creazione di un nuovo certificato e sono documentati in dettaglio [qui]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renewal-of-self-signed-certificate"></a>Rinnovo del certificato autofirmato

Novità. Azure Key Vault si occuperà anche del rinnovo automatico dei certificati autofirmati per gli utenti. Per altre informazioni sulla modifica dei criteri di rilascio e sull'aggiornamento degli attributi dell'azione di durata del certificato, vedere [qui](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

### <a name="troubleshoot"></a>Risoluzione dei problemi
Se il certificato emesso è nello stato "disabilitato" nella portale di Azure, continuare a visualizzare l'operazione relativa al certificato per visualizzare il messaggio di errore relativo al certificato.

### <a name="see-also"></a>Vedere anche
*   [Integrazione di Key Vault con l'autorità di certificazione DigiCert](how-to-integrate-certificate-authority.md)
*   [Esercitazione: Configurare la rotazione automatica dei certificati in Key Vault](tutorial-rotate-certificates.md)