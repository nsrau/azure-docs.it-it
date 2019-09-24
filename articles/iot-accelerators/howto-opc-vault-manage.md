---
title: Come gestire il servizio certificati dell'insieme di credenziali OPC-Azure | Microsoft Docs
description: Gestire i certificati della CA radice dell'insieme di credenziali OPC e le autorizzazioni utente.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203663"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Gestire il servizio certificati dell'insieme di credenziali OPC

Questo articolo illustra le attività amministrative per il servizio di gestione certificati dell'insieme di credenziali OPC in Azure. Sono incluse informazioni su come rinnovare i certificati della CA emittente, su come rinnovare l'elenco di revoche di certificati (CRL) e su come concedere e revocare l'accesso utente.

## <a name="create-or-renew-the-root-ca-certificate"></a>Creare o rinnovare il certificato CA radice

Dopo aver distribuito l'insieme di credenziali OPC, è necessario creare il certificato CA radice. Senza un certificato CA dell'autorità emittente valido, non è possibile firmare o emettere certificati dell'applicazione. Fare riferimento ai [certificati](howto-opc-vault-secure-ca.md#certificates) per gestire i certificati con durate ragionevoli e sicure. Rinnovare un certificato CA dell'emittente dopo la metà della sua durata. Quando si esegue il rinnovo, si consideri anche che la durata configurata di un certificato dell'applicazione appena firmato non deve superare la durata del certificato della CA emittente.
> [!IMPORTANT]
> Il ruolo di amministratore è necessario per creare o rinnovare il certificato della CA emittente.

1. Aprire il servizio certificati all' `https://myResourceGroup-app.azurewebsites.net`indirizzo ed accedere.
2. Passare a **gruppi di certificati**.
3. È presente un gruppo di certificati predefinito. Selezionare **Modifica**.
4. In **Modifica dettagli gruppo di certificati**è possibile modificare il nome del soggetto e la durata dei certificati dell'autorità di certificazione e dell'applicazione. L'oggetto e le durate devono essere impostati solo una volta prima dell'emissione del primo certificato CA. Le modifiche di durata durante le operazioni potrebbero causare una durata incoerente dei certificati emessi e dei CRL.
5. Immettere un oggetto valido, `CN=My CA Root, O=MyCompany, OU=MyDepartment`ad esempio.<br>
   > [!IMPORTANT]
   > Se si modifica l'oggetto, è necessario rinnovare il certificato dell'autorità emittente oppure il servizio non riuscirà a firmare i certificati dell'applicazione. L'oggetto della configurazione viene controllato rispetto al soggetto del certificato dell'autorità emittente attiva. Se gli oggetti non corrispondono, la firma del certificato viene rifiutata.
6. Selezionare **Salva**.
7. Se a questo punto si verifica un errore "accesso negato", le credenziali utente non hanno l'autorizzazione di amministratore per modificare o creare un nuovo certificato radice. Per impostazione predefinita, l'utente che ha distribuito il servizio dispone di ruoli di amministratore e firma con il servizio. Gli altri utenti devono essere aggiunti ai ruoli responsabile approvazione, writer o amministratore, a seconda delle esigenze nella registrazione dell'applicazione Azure Active Directory (Azure AD).
8. Selezionare **Dettagli**. Verranno visualizzate le informazioni aggiornate.
9. Selezionare **Rinnova certificato CA** per emettere il certificato CA del primo emittente o per rinnovare il certificato dell'autorità emittente. Selezionare **OK**.
10. Dopo alcuni secondi, verranno visualizzati **i dettagli del certificato**. Per scaricare la versione più recente del certificato CA e CRL per la distribuzione nelle applicazioni OPC UA, selezionare **autorità** di certificazione o **CRL**.

Il servizio di gestione dei certificati OPC UA è ora pronto per emettere certificati per le applicazioni OPC UA.

## <a name="renew-the-crl"></a>Rinnovare l'elenco CRL

Il rinnovo del CRL è un aggiornamento, che deve essere distribuito alle applicazioni a intervalli regolari. I dispositivi OPC UA, che supportano l'estensione X509 del punto di distribuzione CRL, possono aggiornare direttamente il CRL dall'endpoint del microservizio. Altri dispositivi OPC UA potrebbero richiedere aggiornamenti manuali oppure possono essere aggiornati usando le estensioni push del server GDS (*) per aggiornare gli elenchi di attendibilità con i certificati e i CRL.

Nel flusso di lavoro seguente tutte le richieste di certificati negli stati eliminati vengono revocate nei CRL, che corrispondono al certificato della CA emittente per il quale sono stati rilasciati. Il numero di versione del CRL viene incrementato di 1. <br>
> [!NOTE]
> Tutti i CRL rilasciati sono validi fino alla scadenza del certificato della CA emittente. Questo perché la specifica OPC UA non richiede un modello di distribuzione deterministico obbligatorio per CRL.

> [!IMPORTANT]
> Il ruolo di amministratore è necessario per rinnovare l'elenco CRL dell'emittente.

1. Aprire il servizio certificati all' `https://myResourceGroup.azurewebsites.net`indirizzo ed accedere.
2. Passare alla pagina **gruppi di certificati** .
3. Selezionare **Dettagli**. Verranno visualizzate le informazioni del certificato e del CRL correnti.
4. Selezionare **Aggiorna elenco di REVOCHE CRL (CRL)** per emettere un CRL aggiornato per tutti i certificati dell'autorità emittente attivo nell'archivio dell'insieme di credenziali OPC.
5. Dopo alcuni secondi, verranno visualizzati **i dettagli del certificato**. Per scaricare la versione più recente del certificato CA e CRL per la distribuzione nelle applicazioni OPC UA, selezionare **autorità** di certificazione o **CRL**.

## <a name="manage-user-roles"></a>Gestire i ruoli utente

È possibile gestire i ruoli utente per il microservizio dell'insieme di credenziali OPC nell'applicazione Azure AD Enterprise. Per una descrizione dettagliata delle definizioni di ruolo, vedere [ruoli](howto-opc-vault-secure-ca.md#roles).

Per impostazione predefinita, un utente autenticato nel tenant può accedere al servizio come lettore. I ruoli con privilegi più elevati richiedono la gestione manuale nel portale di Azure o tramite PowerShell.

### <a name="add-user"></a>Aggiungi utente

1. Aprire il Portale di Azure.
2. Passare a **Azure Active Directory** > **applicazioni aziendali**.
3. Scegliere la registrazione del microservizio dell'insieme di credenziali OPC (per impostazione `resourceGroupName-service`predefinita, il).
4. Passare a **utenti e gruppi**.
5. Selezionare **Add User** (Aggiungi utente).
6. Consente di selezionare o invitare l'utente per l'assegnazione a un ruolo specifico.
7. Selezionare il ruolo per gli utenti.
8. Selezionare **Assegna**.
9. Per gli utenti con ruolo di amministratore o responsabile approvazione, continuare ad aggiungere Azure Key Vault criteri di accesso.

### <a name="remove-user"></a>Rimuovi utente

1. Aprire il Portale di Azure.
2. Passare a **Azure Active Directory** > **applicazioni aziendali**.
3. Scegliere la registrazione del microservizio dell'insieme di credenziali OPC (per impostazione `resourceGroupName-service`predefinita, il).
4. Passare a **utenti e gruppi**.
5. Selezionare un utente con un ruolo da rimuovere, quindi selezionare **Rimuovi**.
6. Per rimuovere gli utenti dal ruolo amministratore o responsabile approvazione, rimuoverli anche dai criteri di Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Aggiungere i criteri di accesso utente a Azure Key Vault

Per responsabili approvazione e amministratori sono necessari criteri di accesso aggiuntivi.

Per impostazione predefinita, l'identità del servizio dispone solo di autorizzazioni limitate per l'accesso Key Vault, per impedire l'esecuzione di operazioni o modifiche elevate senza la rappresentazione dell'utente. Le autorizzazioni di base per il servizio sono Get ed list per i segreti e i certificati. Per i segreti, esiste una sola eccezione: il servizio può eliminare una chiave privata dall'archivio segreto dopo che è stata accettata da un utente. Tutte le altre operazioni richiedono autorizzazioni rappresentate dall'utente.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Per un ruolo responsabile approvazione, è necessario aggiungere le autorizzazioni seguenti a Key Vault

1. Aprire il Portale di Azure.
2. Passare all'insieme di credenziali `resourceGroupName`OPC, usato durante la distribuzione.
3. Passare alla Key Vault `resourceGroupName-xxxxx`.
4. Passare a **criteri di accesso**.
5. Selezionare **Aggiungi nuova**.
6. Ignorare il modello. Non esiste un modello che soddisfi i requisiti.
7. Scegliere **Seleziona entità**e selezionare l'utente da aggiungere o invitare un nuovo utente al tenant.
8. Selezionare le **autorizzazioni chiave**seguenti: **Get**, **List**e **Sign**.
9. Selezionare le **autorizzazioni segrete**seguenti: **Get**, **List**, **set**ed **Delete**.
10. Selezionare le autorizzazioni per i **certificati**seguenti: **Ottenere** ed **elencare**.
11. Fare clic su **OK**e selezionare **Salva**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Per un ruolo di amministratore, è necessario aggiungere le autorizzazioni seguenti a Key Vault

1. Aprire il Portale di Azure.
2. Passare all'insieme di credenziali `resourceGroupName`OPC, usato durante la distribuzione.
3. Passare alla Key Vault `resourceGroupName-xxxxx`.
4. Passare a **criteri di accesso**.
5. Selezionare **Aggiungi nuova**.
6. Ignorare il modello. Non esiste un modello che soddisfi i requisiti.
7. Scegliere **Seleziona entità**e selezionare l'utente da aggiungere o invitare un nuovo utente al tenant.
8. Selezionare le **autorizzazioni chiave**seguenti: **Get**, **List**e **Sign**.
9. Selezionare le **autorizzazioni segrete**seguenti: **Get**, **List**, **set**ed **Delete**.
10. Selezionare le autorizzazioni per i **certificati**seguenti: **Ottenere**, **elencare**, **aggiornare**, **creare**e **importare**.
11. Fare clic su **OK**e selezionare **Salva**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Rimuovere i criteri di accesso utente dal Azure Key Vault

1. Aprire il Portale di Azure.
2. Passare all'insieme di credenziali `resourceGroupName`OPC, usato durante la distribuzione.
3. Passare alla Key Vault `resourceGroupName-xxxxx`.
4. Passare a **criteri di accesso**.
5. Trovare l'utente da rimuovere e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire i certificati e gli utenti dell'insieme di credenziali OPC, è possibile:

> [!div class="nextstepaction"]
> [Comunicazione sicura dei dispositivi OPC](howto-opc-vault-secure.md)
