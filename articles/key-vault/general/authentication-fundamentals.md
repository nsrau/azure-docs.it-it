---
title: Nozioni fondamentali sull'autenticazione Azure Key Vault
description: Informazioni sul funzionamento del modello di autenticazione di Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 6de0fc52ae265a47ca7f52d46e5f44b74c1277aa
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530919"
---
# <a name="key-vault-authentication-fundamentals"></a>Nozioni fondamentali sull'autenticazione di Key Vault

Azure Key Vault consente di archiviare e gestire in modo sicuro le credenziali dell'applicazione, ad esempio segreti, chiavi e certificati, in un repository cloud centrale e sicuro. Key Vault Elimina la necessità di archiviare le credenziali nelle applicazioni. Le applicazioni possono eseguire l'autenticazione a Key Vault in fase di esecuzione per recuperare le credenziali.

Come amministratore, è possibile controllare in modo rigoroso quali utenti e applicazioni possono accedere all'insieme di credenziali delle chiavi ed è possibile limitare e controllare le operazioni che eseguono. Questo documento illustra i concetti fondamentali del modello di accesso a Key Vault. E fornisce un livello introduttivo di informazioni e Mostra come è possibile autenticare un utente o un'applicazione nell'insieme di credenziali delle chiavi dall'inizio alla fine.

## <a name="required-knowledge"></a>Informazioni necessarie

In questo documento si presuppone che l'utente abbia familiarità con i concetti seguenti. Se non si ha familiarità con nessuno di questi concetti, seguire i collegamenti della guida prima di procedere.

* [Collegamento](../../active-directory/fundamentals/active-directory-whatis.md) Azure Active Directory
* [Collegamento](./authentication.md#app-identity-and-security-principals) entità di sicurezza

## <a name="key-vault-configuration-steps-summary"></a>Riepilogo della procedura di configurazione Key Vault

1. Registrare l'utente o l'applicazione in Azure Active Directory come entità di sicurezza.
1. Configurare un'assegnazione di ruolo per l'entità di sicurezza in Azure Active Directory.
1. Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'entità di sicurezza.
1. Configurare l'accesso Key Vault firewall all'insieme di credenziali delle chiavi (facoltativo).
1. Testare la capacità dell'entità di sicurezza di accedere a Key Vault.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrare un utente o un'applicazione in Azure Active Directory come entità di sicurezza

Quando un utente o un'applicazione effettua una richiesta all'insieme di credenziali delle chiavi, la richiesta deve prima essere autenticata da Azure Active Directory. Per funzionare, l'utente o l'applicazione deve essere registrata in Azure Active Directory come entità di sicurezza.

Per informazioni su come registrare un utente o un'applicazione in Azure Active Directory, seguire i collegamenti alla documentazione riportata di seguito.
**Assicurarsi di creare una password per la registrazione dell'utente e un segreto client o le credenziali del certificato client per le applicazioni.**

* Registrazione di un utente nel [collegamento](../../active-directory/fundamentals/add-users-azure-active-directory.md) Azure Active Directory
* Registrazione di un'applicazione nel [collegamento](../../active-directory/develop/quickstart-register-app.md) Azure Active Directory

## <a name="assign-your-security-principal-a-role"></a>Assegnare un ruolo all'entità di sicurezza

È possibile usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per assegnare le autorizzazioni alle entità di sicurezza. Queste autorizzazioni sono denominate assegnazioni di ruolo.

Nel contesto di Key Vault, queste assegnazioni di ruolo determinano il livello di accesso dell'entità di sicurezza al piano di gestione (noto anche come piano di controllo) dell'insieme di credenziali delle chiavi. Queste assegnazioni di ruolo non forniscono direttamente l'accesso ai segreti del piano dati, ma forniscono l'accesso per gestire le proprietà di Key Vault. Ad esempio, un utente o un'applicazione a cui è stato assegnato un **ruolo di lettore** non potrà apportare modifiche alle impostazioni del firewall dell'insieme di credenziali delle chiavi, mentre un utente o un'applicazione assegnata a un **ruolo Collaboratore** può apportare modifiche. Nessuno dei due ruoli avrà accesso diretto per eseguire operazioni su segreti, chiavi e certificati, ad esempio la creazione o il recupero del valore fino a quando non viene assegnato l'accesso al piano dati dell'insieme di credenziali delle chiavi. Questo passaggio viene trattato nel passaggio successivo.

>[!IMPORTANT]
> Anche se gli utenti con ruolo di collaboratore o proprietario non hanno accesso per eseguire operazioni sui segreti archiviati nell'insieme di credenziali delle chiavi per impostazione predefinita, i ruoli collaboratore e proprietario, forniscono le autorizzazioni per aggiungere o rimuovere i criteri di accesso ai segreti archiviati in Key Vault. Un utente con queste assegnazioni di ruolo può quindi concedere l'accesso per accedere ai segreti nell'insieme di credenziali delle chiavi. Per questo motivo, è consigliabile che solo gli amministratori abbiano accesso ai ruoli collaboratore o proprietario. Agli utenti e alle applicazioni che devono solo recuperare i segreti dall'insieme di credenziali delle chiavi deve essere concesso il ruolo lettore. **Altri dettagli nella sezione successiva.**

>[!NOTE]
> Quando si assegna un'assegnazione di ruolo a un utente a livello di Azure Active Directory tenant, questo set di autorizzazioni produrrà a tutte le sottoscrizioni, i gruppi di risorse e le risorse nell'ambito dell'assegnazione. Per seguire l'entità dei privilegi minimi, è possibile eseguire questa assegnazione di ruolo in un ambito più granulare. Ad esempio, è possibile assegnare a un utente un ruolo di lettore a livello di sottoscrizione e un ruolo proprietario per un singolo insieme di credenziali delle chiavi. Per eseguire un'assegnazione di ruolo in un ambito più granulare, passare alle impostazioni IAM (Identity Access Management) di una sottoscrizione, un gruppo di risorse o un insieme di credenziali delle chiavi.

* Per ulteriori informazioni sul [collegamento](../../role-based-access-control/built-in-roles.md) ai ruoli di Azure
* Per altre informazioni sull'assegnazione o la rimozione di assegnazioni di ruolo, vedere [collegamento](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'entità di sicurezza

Prima di concedere l'accesso agli utenti e alle applicazioni per accedere a Key Vault, è importante comprendere i diversi tipi di operazioni che possono essere eseguite su un insieme di credenziali delle chiavi. Esistono due tipi principali di operazioni dell'insieme di credenziali delle chiavi, le operazioni del piano di gestione (noto anche come piano di controllo) e le operazioni del piano dati.

Questa tabella mostra diversi esempi delle diverse operazioni controllate dal piano di gestione rispetto al piano dati. Le operazioni che modificano le proprietà dell'insieme di credenziali delle chiavi sono operazioni del piano di gestione. Le operazioni che modificano o recuperano il valore dei segreti archiviati in Key Vault sono operazioni del piano dati.

|Operazioni del piano di gestione (esempi)|Operazioni del piano dati (esempi)|
| --- | --- |
| Creare un insieme di credenziali delle chiavi | Creare una chiave, un segreto, un certificato
| Elimina Key Vault | Eliminare una chiave, un segreto, un certificato
| Aggiungere o rimuovere Key Vault assegnazioni di ruolo | Elencare e ottenere i valori di chiavi, segreti, certificati
| Aggiunta o rimozione di criteri di accesso Key Vault | Chiavi di backup e ripristino, segreti, certificati
| Modificare le impostazioni del firewall Key Vault | Rinnovare chiavi, segreti, certificati
| Modificare le impostazioni di Key Vault Recovery | Ripulire o ripristinare chiavi, segreti e certificati eliminati temporaneamente
| Modificare le impostazioni dei log di diagnostica Key Vault

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Accesso al piano di gestione & le assegnazioni di ruolo Azure Active Directory

Azure Active Directory assegnazioni di ruolo concedono l'accesso per eseguire operazioni del piano di gestione in un insieme di credenziali delle chiavi. Questo accesso viene in genere concesso agli utenti e non alle applicazioni. È possibile limitare le operazioni del piano di gestione che un utente può eseguire modificando l'assegnazione di ruolo di un utente.

Ad esempio, l'assegnazione di un utente a un ruolo di lettore Key Vault a un utente consentirà loro di visualizzare le proprietà dell'insieme di credenziali delle chiavi, ad esempio i criteri di accesso, ma non consentirà di apportare modifiche. Assegnando un utente, un ruolo proprietario consentirà l'accesso completo per modificare le impostazioni del piano di gestione di Key Vault.

Le assegnazioni di ruolo vengono controllate nel pannello controllo di accesso dell'insieme di credenziali delle chiavi (IAM). Se si vuole che un utente specifico possa accedere a un lettore o essere l'amministratore di più risorse di Key Vault, è possibile creare un'assegnazione di ruolo a livello di insieme di credenziali, gruppo di risorse o sottoscrizione e l'assegnazione di ruolo verrà aggiunta a tutte le risorse nell'ambito dell'assegnazione.

L'accesso al piano dati o l'accesso per l'esecuzione di operazioni su chiavi, segreti e certificati archiviati nell'insieme di credenziali delle chiavi può essere aggiunto in uno dei due modi seguenti.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Opzione di accesso al piano dati 1: criteri di accesso Key Vault classici

I criteri di accesso dell'insieme di credenziali delle chiavi consentono a utenti e applicazioni di eseguire operazioni sul piano dati in un insieme di credenziali delle chiavi

> [!NOTE]
> Questo modello di accesso non è compatibile con il controllo degli accessi in base al ruolo di Azure per Key Vault (opzione 2). È necessario sceglierne una. Quando si fa clic sulla scheda criteri di accesso dell'insieme di credenziali delle chiavi, si avrà la possibilità di effettuare questa selezione.

I criteri di accesso classici sono granulari, il che significa che è possibile consentire o negare a ogni singolo utente o applicazione di eseguire singole operazioni all'interno di un insieme di credenziali delle chiavi. Ecco alcuni esempi:

* L'entità di sicurezza 1 può eseguire qualsiasi operazione chiave, ma non è consentita per l'esecuzione di operazioni segrete o di certificati.
* L'entità di sicurezza 2 può elencare e leggere tutte le chiavi, i segreti e i certificati, ma non può eseguire alcuna operazione di creazione, eliminazione o rinnovo.
* L'entità di sicurezza 3 consente di eseguire il backup e il ripristino di tutti i segreti, ma non di leggere il valore dei segreti.

Tuttavia, i criteri di accesso classici non consentono le autorizzazioni a livello di oggetto e le autorizzazioni assegnate vengono applicate all'ambito di un singolo insieme di credenziali delle chiavi. Ad esempio, se si concede l'autorizzazione per i criteri di accesso "Secret Get" a un'entità di sicurezza in un insieme di credenziali delle chiavi specifico, l'entità di sicurezza ha la possibilità di ottenere tutti i segreti nell'insieme di credenziali delle chiavi specifico. Tuttavia, questa autorizzazione "Get Secret" non si estenderà automaticamente ad altri insiemi di credenziali delle chiavi e deve essere assegnata in modo esplicito.

> [!IMPORTANT]
> I criteri di accesso di Key Vault classici e le assegnazioni di ruolo Azure Active Directory sono indipendenti l'uno dall'altro. Se si assegna un'entità di sicurezza, un ruolo di "collaboratore" a livello di sottoscrizione non consentirà automaticamente all'entità di sicurezza di eseguire operazioni del piano dati in ogni insieme di credenziali delle chiavi nell'ambito della sottoscrizione. L'entità di sicurezza deve comunque essere concessa o concedere a se stessi le autorizzazioni dei criteri di accesso per eseguire operazioni del piano dati.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Opzione di accesso al piano dati 2: RBAC di Azure per Key Vault (anteprima)

Un nuovo modo per concedere l'accesso al piano dati dell'insieme di credenziali delle chiavi è tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per Key Vault.

> [!NOTE]
> Questo modello di accesso non è compatibile con i criteri di accesso classici dell'insieme di credenziali delle chiavi indicati in precedenza. È necessario sceglierne una. Quando si fa clic sulla scheda criteri di accesso dell'insieme di credenziali delle chiavi, si avrà la possibilità di effettuare questa selezione.

Key Vault le assegnazioni di ruolo sono un set di assegnazioni di ruolo predefinite di Azure che includono set comuni di autorizzazioni utilizzate per accedere a chiavi, segreti e certificati. Questo modello di autorizzazione Abilita anche funzionalità aggiuntive che non sono disponibili nel modello di criteri di accesso di Key Vault classico.

* È possibile gestire le autorizzazioni RBAC di Azure su larga scala consentendo agli utenti di assegnare questi ruoli a una sottoscrizione, a un gruppo di risorse o a un singolo livello dell'insieme di credenziali delle chiavi. Un utente disporrà delle autorizzazioni del piano dati per tutti gli insiemi di credenziali delle chiavi nell'ambito dell'assegnazione RBAC di Azure. In questo modo si elimina la necessità di assegnare le autorizzazioni dei singoli criteri di accesso per utente/applicazione per ogni insieme di credenziali delle chiavi.

* Le autorizzazioni RBAC di Azure sono compatibili con Privileged Identity Management o PIM. In questo modo è possibile configurare i controlli di accesso just-in-time per i ruoli con privilegi come amministratore Key Vault. Si tratta di una procedura di sicurezza consigliata che segue il principale dei privilegi minimi eliminando l'accesso permanente agli insiemi di credenziali delle chiavi.

Per ulteriori informazioni sul controllo degli accessi in base al ruolo di Azure per Key Vault, vedere i documenti seguenti:

* Controllo RBAC di Azure per il [collegamento](./secure-your-key-vault.md#management-plane-and-azure-rbac) Key Vault
* [Collegamento](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) RBAC di Azure per i ruoli di Key Vault (anteprima)

## <a name="configure-key-vault-firewall"></a>Configurare Key Vault firewall

Per impostazione predefinita, l'insieme di credenziali delle chiavi consente il traffico dalla rete Internet pubblica per inviare Reach The Key Vault tramite un endpoint pubblico. Per un ulteriore livello di sicurezza, è possibile configurare il firewall Azure Key Vault per limitare l'accesso all'endpoint pubblico di Key Vault.

Per abilitare il firewall di Key Vault, fare clic sulla scheda rete nel portale dell'insieme di credenziali delle chiavi e selezionare il pulsante di opzione per consentire l'accesso da: "endpoint privato e reti selezionate". Se si sceglie di abilitare il firewall dell'insieme di credenziali delle chiavi, questi sono i modi in cui è possibile consentire il traffico attraverso il firewall di Key Vault.

* Aggiungere indirizzi IPv4 all'elenco Consenti del firewall di Key Vault. Questa opzione è ottimale per le applicazioni che dispongono di indirizzi IP statici.

* Aggiungere una rete virtuale al firewall di Key Vault. Questa opzione funziona meglio per le risorse di Azure con indirizzi IP dinamici, ad esempio macchine virtuali. È possibile aggiungere risorse di Azure a una rete virtuale e aggiungere la rete virtuale all'elenco Consenti del firewall del Key Vault. Questa opzione Usa un endpoint del servizio che è un indirizzo IP privato all'interno della rete virtuale. Questo offre un livello aggiuntivo di protezione, in modo che nessun traffico tra l'insieme di credenziali delle chiavi e la rete virtuale venga instradato attraverso la rete Internet pubblica. Per ulteriori informazioni sull'endpoint di servizio, vedere la documentazione seguente. [collegamento](./network-security.md)

* Aggiungere una connessione di collegamento privato all'insieme di credenziali delle chiavi. Questa opzione connette la rete virtuale direttamente a una particolare istanza dell'insieme di credenziali delle chiavi, inserendo in effetti l'insieme di credenziali delle chiavi all'interno della rete virtuale. Per altre informazioni sulla configurazione di una connessione di un endpoint privato a Key Vault, vedere il [collegamento](./private-link-service.md) seguente.

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testare la capacità dell'entità servizio di accedere all'insieme di credenziali delle chiavi

Dopo aver seguito tutti i passaggi precedenti, sarà possibile impostare e recuperare i segreti dall'insieme di credenziali delle chiavi.

### <a name="authentication-process-for-users-examples"></a>Processo di autenticazione per gli utenti (esempi)

* Gli utenti possono accedere al portale di Azure per usare Key Vault. [Guida introduttiva al portale Key Vault](./quick-create-portal.md)

* L'utente può usare l'interfaccia della riga di comando di Azure per usare Key Vault. [Guida introduttiva di Key Vault CLI di Azure](./quick-create-cli.md)

* L'utente può usare Azure PowerShell per usare Key Vault. [Guida introduttiva a Key Vault Azure PowerShell](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory processo di autenticazione per applicazioni o servizi (esempi)

* Un'applicazione fornisce un segreto client e un ID client in una funzione per ottenere un token di Azure Active Directory. 

* Un'applicazione fornisce un certificato per ottenere un token di Azure Active Directory. 

* Una risorsa di Azure usa l'autenticazione MSI per ottenere un token di Azure Active Directory. 

* Altre informazioni sul [collegamento](../../active-directory/managed-identities-azure-resources/overview.md) di autenticazione MSI

### <a name="authentication-process-for-application-python-example"></a>Processo di autenticazione per l'applicazione (esempio Python)

Usare l'esempio di codice seguente per verificare se l'applicazione è in grado di recuperare un segreto da Key Vault usando l'entità servizio configurata.

>[!NOTE]
>Questo esempio è solo a scopo dimostrativo e di test. **non usare l'autenticazione del segreto client nell'ambiente di produzione** Non si tratta di una procedura di progettazione sicura. È consigliabile usare il certificato client o l'autenticazione MSI come procedura consigliata.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate sull'autenticazione dell'insieme di credenziali delle chiavi, vedere il documento seguente. [Autenticazione dell'insieme di credenziali delle chiavi](./authentication.md)
