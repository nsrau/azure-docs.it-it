---
title: Eseguire l'autenticazione con Azure Key Vault
description: Informazioni su come eseguire l'autenticazione con Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589983"
---
# <a name="authenticate-to-azure-key-vault"></a>Eseguire l'autenticazione con Azure Key Vault

## <a name="overview"></a>Panoramica

Azure Key Vault è una soluzione di gestione dei segreti che consente di centralizzare l'archiviazione dei segreti delle applicazioni e di controllarne la distribuzione. Azure Key Vault elimina la necessità di archiviare le credenziali nelle applicazioni. L'applicazione può eseguire l'autenticazione con Key Vault per recuperare le credenziali necessarie. Questo documento riguarda le nozioni di base dell'autenticazione con Key Vault.

Questo documento illustra il funzionamento dell'autenticazione con Key Vault. Descrive il flusso di autenticazione, mostra come concedere l'accesso alla propria istanza di Key Vault e include un'esercitazione per recuperare un segreto archiviato in Key Vault da un'applicazione Python di esempio.

Il documento tratta gli argomenti seguenti:

* Concetti chiave
* Registrazione dell'entità di sicurezza
* Informazioni sul flusso di autenticazione con Key Vault
* Concedere all'entità servizio l'accesso a Key Vault
* Esercitazione (Python)

## <a name="key-concepts"></a>Concetti chiave

### <a name="azure-active-directory-concepts"></a>Concetti relativi ad Azure Active Directory

* Azure Active Directory (Azure AD): il servizio di gestione di identità e accessi basato sul cloud di Microsoft, che consente ai dipendenti di accedere e usare le risorse

* Definizione di ruolo: una raccolta di autorizzazioni.  AAD include ruoli standard (Proprietario, Collaboratore o Lettore) che contengono i livelli di autorizzazioni per eseguire operazioni come lettura, scrittura ed eliminazione delle risorse di Azure. I ruoli possono anche essere definizioni personalizzate create dagli utenti con autorizzazioni granulari specifiche.

* Registrazione dell'applicazione: quando si registra un'applicazione Azure AD, nel tenant di Azure AD vengono creati due oggetti, ovvero un oggetto applicazione e un oggetto entità servizio. L'oggetto applicazioni può essere considerato come la rappresentazione globale dell'applicazione per l'uso tra tutti i tenant, mentre l'entità servizio come la rappresentazione locale per l'uso in uno specifico tenant.

### <a name="security-principal-concepts"></a>Concetti relativi all'entità di sicurezza

* Entità di sicurezza: un oggetto che rappresenta un utente, un gruppo, un'entità servizio o un'identità gestita che richiede l'accesso alle risorse di Azure.

* Utente: un soggetto che dispone di un profilo in Azure Active Directory.

* Gruppo: un set di utenti creato in Azure Active Directory. Quando si assegna un ruolo a un gruppo, tutti gli utenti all'interno di tale gruppo dispongono del ruolo appropriato.

* Entità servizio: un'identità di sicurezza utilizzata da applicazioni o servizi per accedere a specifiche risorse di Azure. Può essere considerata come un'identità utente (nome utente e password o certificato) per un'applicazione.

* Identità gestita: un'identità in Azure Active Directory che viene gestita automaticamente da Azure.

* ID oggetto (ID client): un identificatore univoco generato da Azure AD che viene associato a un'entità servizio durante il provisioning iniziale.

## <a name="security-principal-registration"></a>Registrazione dell'entità di sicurezza

1. L'amministratore registra un utente o un'applicazione (entità servizio) in Azure Active Directory.

2. L'amministratore crea un'istanza di Azure Key Vault e configura i criteri di accesso (ACL).

3. (Facoltativo)e L'amministratore configura il firewall di Azure Key Vault.

![IMAGE](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Informazioni sul flusso di autenticazione di Key Vault

1. Un'entità servizio esegue una chiamata per l'autenticazione con AAD. Questa operazione può essere eseguita in diversi modi:
    * Un utente può accedere al portale di Azure usando un nome utente e una password.
    * Un'applicazione usa un ID client e presenta un segreto client o un certificato client ad AAD
    * Una risorsa di Azure, ad esempio una macchina virtuale, ha un'entità del servizio gestita assegnata e contatta l'endpoint REST IMDS per ottenere un token di accesso.

2. Se l'autenticazione con AAD riesce, all'entità servizio verrà concesso un token OAuth.
3. L'entità servizio effettua una chiamata a Key Vault.
4. Il firewall di Azure Key Vault determina se autorizzare o meno la chiamata.
    * Scenario 1: Il firewall di Key Vault è disabilitato, l'endpoint pubblico (URI) di Key Vault è raggiungibile da Internet pubblico. La chiamata viene autorizzata.
    * Scenario 2: Il chiamante è un servizio attendibile di Azure Key Vault. Determinati servizi di Azure possono ignorare il firewall di Key Vault se l'opzione corrispondente è selezionata. [Elenco di servizi attendibili di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Scenario 3: Il chiamante è presente nel firewall di Azure Key Vault in base a indirizzo IP, rete virtuale o endpoint di servizio.
    * Scenario 4: Il chiamante può raggiungere Azure Key Vault tramite una connessione di collegamento privato configurata.
    * Scenario 5: Il chiamante non è autorizzato e viene restituita una risposta di operazione non consentita.
5. Key Vault effettua una chiamata ad AAD per convalidare il token di accesso dell'entità servizio.
6. Key Vault controlla se l'entità servizio ha autorizzazioni di accesso sufficienti per eseguire l'operazione richiesta, che in questo esempio è il recupero del segreto.
7. Key Vault fornisce il segreto all'entità servizio.

![IMAGE](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Concedere all'entità servizio l'accesso a Key Vault

1. Creare un'entità servizio se non è già disponibile. [Creare un'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Aggiungere un'assegnazione di ruolo all'entità servizio nelle impostazioni di gestione delle identità e degli accessi di Azure Key Vault. È possibile aggiungere i ruoli preassegnati di Proprietario, Collaboratore o Lettore. È anche possibile creare ruoli personalizzati per l'entità servizio. È consigliabile seguire il principio dei privilegi minimi e fornire solo l'accesso minimo necessario per l'entità servizio. 
3.  Configurare il firewall di Key Vault. È possibile lasciare disabilitato il firewall di Key Vault e consentire l'accesso da Internet pubblico (scelta meno sicura ma più facile da configurare). È anche possibile limitare l'accesso a specifici intervalli IP, endpoint di servizio, reti virtuali o endpoint privati (scelta più sicura).
4.  Aggiungere un criterio di accesso per l'entità servizio, ovvero un elenco di operazioni che l'entità servizio può eseguire in Key Vault. È consigliabile usare il principio dei privilegi minimi e limitare le operazioni che l'entità servizio può eseguire. Se tuttavia non si forniscono autorizzazioni sufficienti, all'entità servizio verrà negato l'accesso.

## <a name="tutorial"></a>Esercitazione

Questa esercitazione illustra come configurare un'entità servizio per l'autenticazione con Key Vault e il recupero di un segreto. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Parte 1:  Creare un'entità servizio nel portale di Azure

1. Accedere al Portale di Azure
1. Cercare Azure Active Directory
1. Fare clic sulla scheda "Registrazioni app"
1. Fare clic su "+ Nuova registrazione"
1. Creare un nome per l'entità servizio
1. Selezionare Registra.

A questo punto è disponibile un'entità servizio registrata. È possibile visualizzarla selezionando "Registrazioni app". All'entità servizio verrà ora assegnato un GUID dell'ID client, paragonabile a un "nome utente" per l'entità servizio. Ora è necessario cerare una "password" per l'entità servizio e a questo scopo è possibile usare un segreto client o un certificato client. Si noti che l'uso di un segreto client per l'autenticazione non è sicuro ed è consigliabile scegliere questa opzione solo a scopo di test. Questa esercitazione illustra come usare un certificato client.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Parte 2: Creare un certificato client per l'entità servizio

1. Creare un certificato

    * Opzione 1: Creare un certificato usando [OpenSSL](https://www.openssl.org/) (solo a scopo di test, non usare certificati autofirmati in produzione)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Opzione 2: Creare un certificato usando Key Vault. [Creare un certificato in Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Scaricare il certificato in formato PEM
1. Accedere al portale di Azure e passare ad Azure Active Directory
1. Fare clic su "Registrazioni app"
1. Selezionare l'entità servizio creata nella parte 1.
1. Fare clic sulla scheda "Certificati e segreti" dell'entità servizio
1. Caricare il certificato usando il pulsante "Carica certificato"

### <a name="part-3-configure-an-azure-key-vault"></a>Parte 3: Configurare Azure Key Vault

1. Creare un [collegamento](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) ad Azure Key Vault

2. Configurare le autorizzazioni di gestione delle identità e degli accessi di Key Vault
    1. Passare a Key Vault
    1. Selezionare la scheda "Controllo di accesso (IAM)"
    1. Fare clic su Aggiungi un'assegnazione ruolo
    1. Selezionare il ruolo "Collaboratore" nell'elenco a discesa
    1. Immettere il nome o l'ID client dell'entità servizio creata
    1. Fare clic su "Visualizza le assegnazioni di ruolo" per verificare che l'entità servizio sia elencata

3. Configurare le autorizzazioni di accesso a Key Vault
    1. Passare a Key Vault
    1. Selezionare la scheda "Criteri di accesso" in "Impostazioni"
    1. Selezionare il collegamento "+ Aggiungi un criterio di accesso"
    1. Nell'elenco a discesa Autorizzazioni dei segreti selezionare le autorizzazioni "Ottieni" ed "Elenca".
    1. Selezionare l'entità servizio in base al nome o all'ID client.
    1. Selezionare "Aggiungi"
    1. Selezionare "Salva"

4. Creare un segreto in Key Vault.
    1. Passare a Key Vault
    1. Fare clic sulla scheda "Segreti" in Impostazioni
    1. Fare clic su "+ Genera/Importa"
    1. Creare un nome per il segreto, in questo esempio, "test"
    1. Creare un valore per il segreto, in questo esempio "password123"

A questo punto, quando si esegue il codice dal computer locale, è possibile eseguire l'autenticazione con Key Vault ottenendo un token di accesso presentando l'ID client e il percorso del certificato.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Parte 4: Recuperare il segreto da Azure Key Vault in un'applicazione (Python)

Usare l'esempio di codice seguente per verificare se l'applicazione è in grado di recuperare un segreto da Key Vault usando l'entità servizio configurata. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGE](../media/authentication-3.png)


## <a name="next-steps"></a>Passaggi successivi

1. Informazioni su come risolvere gli errori di autenticazione di Key Vault. [Guida alla risoluzione dei problemi di Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
