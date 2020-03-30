---
title: Come gestire il servizio certificati OPC Vault - Azure Documenti Microsoft
description: Gestire i certificati CA radice del Vault OPC e le autorizzazioni utente.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203663"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Gestire il servizio certificati OPC Vault

Questo articolo illustra le attività amministrative per il servizio di gestione dei certificati di OPC Vault in Azure.This article explains the administrative tasks for the OPC Vault certificate management service in Azure. Sono incluse informazioni su come rinnovare i certificati della CA dell'autorità emittente, su come rinnovare l'elenco di revoche di certificati (CRL) e su come concedere e revocare l'accesso utente.

## <a name="create-or-renew-the-root-ca-certificate"></a>Creare o rinnovare il certificato della CA radice

Dopo aver distribuito OPC Vault, è necessario creare il certificato DELLA CA radice. Senza un certificato CA dell'autorità emittente valido, non è possibile firmare o emettere certificati dell'applicazione. Fare riferimento a [Certificati](howto-opc-vault-secure-ca.md#certificates) per gestire i certificati con durate ragionevoli e sicure. Rinnovare un certificato CA dell'autorità emittente dopo metà del suo ciclo di vita. Al momento del rinnovo, tenere presente anche che la durata configurata di un certificato dell'applicazione appena firmato non deve superare la durata del certificato della CA dell'autorità di certificazione dell'autorità di certificazione dell'autorità emittente.
> [!IMPORTANT]
> Il ruolo Amministratore è necessario per creare o rinnovare il certificato CA dell'autorità emittente.

1. Aprire il servizio `https://myResourceGroup-app.azurewebsites.net`certificati all'indirizzo e accedere.
2. Passare a **Gruppi di certificati**.
3. È elencato un gruppo di certificati predefinito. Selezionare **Modifica**.
4. In **Modifica dettagli gruppo di certificati**è possibile modificare il nome del soggetto e la durata dei certificati dell'autorità di certificazione e dell'applicazione. Il soggetto e le durate devono essere impostati una sola volta prima dell'emissione del primo certificato CA. Le modifiche di durata durante le operazioni potrebbero comportare durate incoerenti di certificati e CRL emessi.
5. Immettere un oggetto valido `CN=My CA Root, O=MyCompany, OU=MyDepartment`(ad esempio, ).<br>
   > [!IMPORTANT]
   > Se si modifica l'oggetto, è necessario rinnovare il certificato dell'autorità di certificazione, altrimenti il servizio non riuscirà a firmare i certificati dell'applicazione. L'oggetto della configurazione viene confrontato con l'oggetto del certificato dell'autorità emittente attiva. Se i soggetti non corrispondono, la firma del certificato viene rifiutata.
6. Selezionare **Salva**.
7. Se si verifica un errore "proibito" a questo punto, le credenziali utente non dispongono dell'autorizzazione di amministratore per modificare o creare un nuovo certificato radice. Per impostazione predefinita, l'utente che ha distribuito il servizio dispone di ruoli di amministratore e di firma con il servizio. Altri utenti devono essere aggiunti ai ruoli Approvatore, Writer o Amministratore, a seconda delle esigenze nella registrazione dell'applicazione Azure Active Directory (Azure AD).
8. Selezionare **Dettagli**. Questo dovrebbe mostrare le informazioni aggiornate.
9. Selezionare **Rinnova certificato CA** per emettere il primo certificato CA dell'autorità emittente o per rinnovare il certificato dell'autorità emittente. Quindi selezionare **OK**.
10. Dopo alcuni secondi, verranno visualizzati I dettagli **del certificato**. Per scaricare il certificato CA e il CRL più recenti per la distribuzione nelle applicazioni OPC UA, selezionare **Issuer** o **Crl**.

Ora il servizio di gestione dei certificati OPC UA è pronto per rilasciare certificati per le applicazioni OPC UA.

## <a name="renew-the-crl"></a>Rinnovare il CRL

Il rinnovo del CRL è un aggiornamento, che deve essere distribuito alle applicazioni a intervalli regolari. I dispositivi OPC UA, che supportano l'estensione X509 del punto di distribuzione CRL, possono aggiornare direttamente il CRL dall'endpoint del microservizio. Altri dispositivi OPC UA potrebbero richiedere aggiornamenti manuali o possono essere aggiornati utilizzando le estensioni push del server GDS ( ) per aggiornare gli elenchi di attendibilità con i certificati e CRL.

Nel flusso di lavoro seguente, tutte le richieste di certificato negli stati eliminati vengono revocate nei CRL, che corrispondono al certificato della CA dell'autorità emittente per cui sono state emesse. Il numero di versione del CRL viene incrementato di 1. <br>
> [!NOTE]
> Tutti i CRL emessi sono validi fino alla scadenza del certificato CA dell'autorità emittente. Ciò è dovuto al fatto che la specifica OPC UA non richiede un modello di distribuzione deterministico obbligatorio per il CRL.

> [!IMPORTANT]
> Il ruolo di amministratore è necessario per rinnovare il CRL dell'autorità emittente.

1. Aprire il servizio `https://myResourceGroup.azurewebsites.net`certificati all'indirizzo e accedere.
2. Passare alla pagina Gruppi di **certificati.**
3. Selezionare **Dettagli**. Verrà visualizzata la pagina del certificato e delle informazioni CRL correnti.
4. Selezionare **Aggiorna elenco di revoche CRL (CRL)** per emettere un CRL aggiornato per tutti i certificati dell'autorità di certificazione attivi nell'archivio del Vault OPC.
5. Dopo alcuni secondi, verranno visualizzati I dettagli **del certificato**. Per scaricare il certificato CA e il CRL più recenti per la distribuzione nelle applicazioni OPC UA, selezionare **Issuer** o **Crl**.

## <a name="manage-user-roles"></a>Gestire ruoli utente

I ruoli utente per il microservizio OPC Vault vengono gestiti nell'applicazione aziendale di Azure AD. Per una descrizione dettagliata delle definizioni di ruolo, vedere [Ruoli](howto-opc-vault-secure-ca.md#roles).

Per impostazione predefinita, un utente autenticato nel tenant può accedere al servizio come lettore. Ruoli con privilegi più elevati richiedono la gestione manuale nel portale di Azure o tramite PowerShell.Higher privileged roles require manual management in the Azure portal, or by using PowerShell.

### <a name="add-user"></a>Add User

1. Aprire il portale di Azure.
2. Passare ad **Applicazioni Azure Active Directory** > **Enterprise**.
3. Scegliere la registrazione del microservizio OPC Vault `resourceGroupName-service`(per impostazione predefinita, il ).
4. Passare a **Utenti e gruppi**.
5. Selezionare **Aggiungi utente**.
6. Selezionare o invitare l'utente per l'assegnazione a un ruolo specifico.
7. Selezionare il ruolo per gli utenti.
8. Selezionare **Assegna**.
9. Per gli utenti nel ruolo Amministratore o Responsabile approvazione, continuare ad aggiungere criteri di accesso dell'archivio delle chiavi di Azure.For users in the Administrator or Approver role, continue to add Azure Key Vault access policies.

### <a name="remove-user"></a>Rimuovi utente

1. Aprire il portale di Azure.
2. Passare ad **Applicazioni Azure Active Directory** > **Enterprise**.
3. Scegliere la registrazione del microservizio OPC Vault `resourceGroupName-service`(per impostazione predefinita, il ).
4. Passare a **Utenti e gruppi**.
5. Selezionare un utente con un ruolo da rimuovere e quindi selezionare **Rimuovi**.
6. Per gli utenti rimossi nel ruolo Amministratore o Approvatore, rimuoverli anche dai criteri dell'insieme di credenziali delle chiavi di Azure.For removed users in the Administrator or Approver role, also remove them from Azure Key Vault policies.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Aggiungere criteri di accesso utente all'insieme di credenziali delle chiavi di AzureAdd user access policy to Azure Key Vault

Sono necessari criteri di accesso aggiuntivi per i responsabili approvazione e gli amministratori.

Per impostazione predefinita, l'identità del servizio dispone solo di autorizzazioni limitate per accedere all'insieme di credenziali delle chiavi, per impedire che le operazioni con privilegi elevati o le modifiche vengano eseguite senza la rappresentazione dell'utente. Le autorizzazioni di base del servizio sono Get ed Elenco, sia per i segreti che per i certificati. Per i segreti, è presente una sola eccezione: il servizio può eliminare una chiave privata dall'archivio segreto dopo che è stata accettata da un utente. Tutte le altre operazioni richiedono autorizzazioni rappresentate dall'utente.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Per un ruolo di responsabile approvazione, è necessario aggiungere le autorizzazioni seguenti all'insieme di credenziali delle chiavi

1. Aprire il portale di Azure.
2. Vai al tuo `resourceGroupName`OPC Vault , utilizzato durante la distribuzione.
3. Passare all'insieme `resourceGroupName-xxxxx`di credenziali delle chiavi .
4. Passare a **Criteri di accesso**.
5. Selezionare **Aggiungi nuova**.
6. Saltare il modello. Non esiste alcun modello che soddisfi i requisiti.
7. Scegliere **Seleziona entità**e selezionare l'utente da aggiungere oppure invitare un nuovo utente al tenant.
8. Selezionare le **seguenti autorizzazioni chiave**: **Get**, **List**e **Sign**.
9. Selezionare le seguenti **autorizzazioni segrete**: **Get**, **List**, **Set**e **Delete**.
10. Selezionare le seguenti **autorizzazioni per**i certificati : **Get** ed **List**.
11. Selezionare **OK**e selezionare **Salva**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Per un ruolo di amministratore, è necessario aggiungere le seguenti autorizzazioni all'insieme di credenziali delle chiavi

1. Aprire il portale di Azure.
2. Vai al tuo `resourceGroupName`OPC Vault , utilizzato durante la distribuzione.
3. Passare all'insieme `resourceGroupName-xxxxx`di credenziali delle chiavi .
4. Passare a **Criteri di accesso**.
5. Selezionare **Aggiungi nuova**.
6. Saltare il modello. Non esiste alcun modello che soddisfi i requisiti.
7. Scegliere **Seleziona entità**e selezionare l'utente da aggiungere oppure invitare un nuovo utente al tenant.
8. Selezionare le **seguenti autorizzazioni chiave**: **Get**, **List**e **Sign**.
9. Selezionare le seguenti **autorizzazioni segrete**: **Get**, **List**, **Set**e **Delete**.
10. Selezionare le seguenti **autorizzazioni per i certificati**: **Get**, **List**, **Update**, **Create**e **Import**.
11. Selezionare **OK**e selezionare **Salva**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Rimuovere i criteri di accesso utente dall'insieme di credenziali delle chiavi di AzureRemove user access policy from Azure Key Vault

1. Aprire il portale di Azure.
2. Vai al tuo `resourceGroupName`OPC Vault , utilizzato durante la distribuzione.
3. Passare all'insieme `resourceGroupName-xxxxx`di credenziali delle chiavi .
4. Passare a **Criteri di accesso**.
5. Individuare l'utente da rimuovere e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come gestire i certificati e gli utenti di OPC Vault, è possibile:

> [!div class="nextstepaction"]
> [Comunicazione sicura dei dispositivi OPC](howto-opc-vault-secure.md)
