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
ms.openlocfilehash: 4420e5b0d895f8ea30dbd39fc50dd7480d57d086
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996004"
---
# <a name="how-to-manage-the-opc-vault-certificate-service"></a>Come gestire il servizio certificati dell'insieme di credenziali OPC

Questo articolo illustra le attività amministrative per il servizio di gestione certificati dell'insieme di credenziali OPC in Azure, come rinnovare i certificati della CA emittente, come rinnovare l'elenco di revoche di certificati (CRL) e come concedere e revocare l'accesso utente.

## <a name="create-or-renew-the-root-ca-certificate"></a>Creare o rinnovare il certificato CA radice

Per creare il certificato CA radice è un passaggio obbligatorio dopo la distribuzione. Senza un certificato CA dell'autorità emittente valido, non è possibile firmare ed emettere certificati di applicazione.<br>Vedere il capitolo sulle durate dei [certificati](howto-opc-vault-secure-ca.md#certificates) per gestire i certificati con durate ragionevoli e sicure. Un certificato CA dell'autorità emittente deve essere rinnovato dopo la metà della sua durata, ma non in un secondo momento prima che la durata configurata di un certificato dell'applicazione appena firmato superi la durata del certificato dell'autorità emittente.<br>
> [!IMPORTANT]
> Il ruolo ' Administrator ' è necessario per creare o rinnovare il certificato CA dell'autorità emittente.

1. Aprire il servizio certificati all' `https://myResourceGroup-app.azurewebsites.net` indirizzo ed accedere.
2. Passare alla pagina `Certificate Groups`.
3. È elencato un `Default` gruppo di certificati. Fare clic su `Edit`.
4. In `Edit Certificate Group Details` è possibile modificare il nome del soggetto e la durata dei certificati dell'autorità di certificazione e dell'applicazione.<br>L'oggetto e le durate devono essere impostati solo una volta prima dell'emissione del primo certificato CA. Le modifiche di durata durante le operazioni possono determinare una durata incoerente dei certificati emessi e dei CRL.
5. Immettere un oggetto valido, ad esempio `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Per modificare il soggetto è necessario rinnovare il certificato dell'autorità emittente oppure il servizio non riuscirà a firmare i certificati dell'applicazione. L'oggetto della configurazione è l'integrità verificata rispetto al soggetto del certificato dell'autorità emittente attiva. Se gli oggetti non corrispondono, la firma del certificato viene rifiutata.
6. Fare clic sul `Save` pulsante.
7. Se a questo punto si raggiunge un errore ' Forbidden ', le credenziali utente non hanno l'autorizzazione di amministratore per modificare o creare un nuovo certificato radice. Per impostazione predefinita, l'utente che ha distribuito il servizio dispone di ruoli di amministratore e firma con il servizio, altri utenti devono essere aggiunti ai ruoli ' responsabile approvazione ',' Writer ' o ' amministratore ' nel modo appropriato nella registrazione dell'applicazione AzureAD.
8. Fare clic sul `Details` pulsante. `View Certificate Group Details` Deve visualizzare le informazioni aggiornate.
9. Fare clic sul `Renew CA Certificate` pulsante per rilasciare il primo certificato CA dell'autorità emittente o per rinnovare il certificato dell'autorità emittente. Premere `Ok` per continuare.
10. Dopo alcuni secondi `Certificate Details` viene visualizzato. Premere `Issuer` o`Crl` per scaricare il certificato della CA più recente e il CRL per la distribuzione nelle applicazioni OPC UA.
11. Il servizio di gestione dei certificati OPC UA è ora pronto per emettere certificati per le applicazioni OPC UA.

## <a name="renew-the-crl"></a>Rinnovare l'elenco CRL

Il rinnovo dell'elenco di revoche di certificati (CRL) è un aggiornamento, che deve essere distribuito alle applicazioni a intervalli regolari. I dispositivi OPC UA, che supportano l'estensione X509 del punto di distribuzione CRL, possono aggiornare direttamente il CRL dall'endpoint del microservizio. Altri dispositivi OPC UA possono richiedere aggiornamenti manuali o nel migliore dei casi, possono essere aggiornati usando le estensioni push del server GDS (*) per aggiornare gli elenchi di attendibilità con i certificati e i CRL.

Nel flusso di lavoro seguente tutte le richieste di certificati negli stati eliminati vengono revocate nei CRL, che corrispondono al certificato della CA emittente per cui sono stati rilasciati. Il numero di versione del CRL viene incrementato di 1. <br>
> [!NOTE]
> Tutti i CRL rilasciati sono validi fino alla scadenza del certificato della CA emittente, perché la specifica OPC UA non richiede un modello di distribuzione deterministico obbligatorio per CRL.

> [!IMPORTANT]
> Il ruolo ' Administrator ' è necessario per rinnovare l'elenco CRL dell'emittente.

1. Aprire il servizio certificati all' `https://myResourceGroup.azurewebsites.net` indirizzo ed accedere.
2. Passare alla pagina `Certificate Groups`.
3. Fare clic sul `Details` pulsante. `View Certificate Group Details` Deve visualizzare il certificato corrente e le informazioni CRL.
4. Fare clic sul `Update CRL Revocation List(CRL)` pulsante per emettere un CRL aggiornato per tutti i certificati dell'autorità emittente attiva nell'archivio dell'insieme di credenziali OPC.
5. Dopo alcuni secondi `Certificate Details` viene visualizzato. Premere `Issuer` o`Crl` per scaricare il certificato della CA più recente e il CRL per la distribuzione nelle applicazioni OPC UA.

## <a name="manage-user-roles"></a>Gestire i ruoli utente

I ruoli utente per il microservizio dell'insieme di credenziali OPC sono gestiti nell'applicazione Azure Active Directory Enterprise.

Per una descrizione dettagliata delle definizioni di ruolo, vedere la sezione [ruoli](howto-opc-vault-secure-ca.md#roles) .

Per impostazione predefinita, un utente autenticato nel tenant può accedere al servizio come ' Reader '. I ruoli con privilegi più elevati richiedono la gestione manuale nel portale di Azure o tramite PowerShell.

### <a name="add-user"></a>Aggiungi utente

1. Aprire la portale di Azure all' `portal.azure.com`indirizzo.
2. Passare a `Azure Active Directory`/`Enterprise applications`.
3. Scegliere la registrazione del microservizio dell'insieme di credenziali OPC, per `resourceGroupName-service`impostazione predefinita.
4. Accedere a `Users and Groups`.
5. Fare clic su `Add User`.
6. Consente di selezionare o invitare l'utente per l'assegnazione a un ruolo specifico.
7. Selezionare il ruolo per gli utenti.
8. Premere il `Assign` pulsante.
9. Per gli utenti `Administrator` nel `Approver` ruolo o, continuare ad aggiungere Azure Key Vault criteri di accesso.

### <a name="remove-user"></a>Rimuovi utente

1. Aprire la portale di Azure all' `portal.azure.com`indirizzo.
2. Passare a `Azure Active Directory`/`Enterprise applications`.
3. Scegliere la registrazione del microservizio dell'insieme di credenziali OPC, per `resourceGroupName-service`impostazione predefinita.
4. Accedere a `Users and Groups`.
5. Selezionare un utente con un ruolo da rimuovere.
6. Premere il `Remove` pulsante.
7. Rimuovere gli amministratori e i responsabili approvazione rimossi anche dai criteri di Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Aggiungere i criteri di accesso utente a Azure Key Vault

Per responsabili **approvazione** e **amministratori**sono necessari criteri di accesso aggiuntivi.

Per impostazione predefinita, l'identità del servizio dispone solo di autorizzazioni limitate per accedere a Key Vault per impedire l'esecuzione di operazioni o modifiche elevate senza la rappresentazione dell'utente. Le autorizzazioni di base per `Get` il `List` servizio sono e per i segreti e i certificati. Per i segreti, esiste solo un'eccezione, il servizio può `Delete` una chiave privata dell'archivio Secret una volta accettato da un utente. Tutte le altre operazioni richiedono autorizzazioni rappresentate dall'utente.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Per un **ruolo responsabile approvazione** è necessario aggiungere le autorizzazioni seguenti a Key Vault:

1. Aprire la portale di Azure all' `portal.azure.com`indirizzo.
2. Passare all'insieme di credenziali `resourceGroupName`OPC usato durante la distribuzione.
3. Passare al Key Vault `resourceGroupName-xxxxx`.
4. Passare a `Access Policies`.
5. Fare clic su `Add new`.
6. Ignorare il modello. non è presente alcun modello, che soddisfa i requisiti.
7. Fare clic `Select Principal` su e selezionare l'utente da aggiungere o invitare un nuovo utente nel tenant.
8. Controllare `Key permissions` :`Get`e soprattutto`Sign`. `List`
9. Controllare `Secret permissions`: `Get`, `List` e.`Delete` `Set`
10. Controllare `Certificate permissions`: `Get`e .`List`
11. Fare clic su `Ok`.
12. `Save`modifiche.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Per un **ruolo di amministratore** , è necessario aggiungere le autorizzazioni seguenti a Key Vault:

1. Aprire la portale di Azure all' `portal.azure.com`indirizzo.
2. Passare all'insieme di credenziali `resourceGroupName`OPC usato durante la distribuzione.
3. Passare al Key Vault `resourceGroupName-xxxxx`.
4. Passare a `Access Policies`.
5. Fare clic su `Add new`.
6. Ignorare il modello. non è presente alcun modello, che soddisfa i requisiti.
7. Fare clic `Select Principal` su e selezionare l'utente da aggiungere o invitare un nuovo utente nel tenant.
8. Controllare `Key permissions` :`Get`e soprattutto`Sign`. `List`
9. Controllare `Secret permissions`: `Get`, `List` e.`Delete` `Set`
10. Controllare `Certificate permissions`: `Get`, `List`,e`Import`. `Update` `Create`
11. Fare clic su `Ok`.
12. `Save`modifiche.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Rimuovere i criteri di accesso utente dal Azure Key Vault

1. Aprire la portale di Azure all' `portal.azure.com`indirizzo.
2. Passare all'insieme di credenziali `resourceGroupName`OPC usato durante la distribuzione.
3. Passare al Key Vault `resourceGroupName-xxxxx`.
4. Passare a `Access Policies`.
5. Trovare l'utente da rimuovere e fare clic `... / Delete` su per eliminare l'accesso utente.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire i certificati e gli utenti dell'insieme di credenziali OPC, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Comunicazione sicura dei dispositivi OPC](howto-opc-vault-secure.md)
