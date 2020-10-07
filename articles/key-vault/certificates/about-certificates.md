---
title: Informazioni sui certificati di Azure Key Vault - Azure Key Vault
description: Panoramica dell'interfaccia REST e dei certificati di Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e7bae2ad19aaf4f1c93d8d2bdefa7fa9f0414860
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88923688"
---
# <a name="about-azure-key-vault-certificates"></a>Informazioni sui certificati di Azure Key Vault

La funzionalità Certificati dell'insieme di credenziali delle chiavi supporta la gestione di certificati X509 e consente di eseguire le operazioni seguenti:  

-   Il proprietario di un certificato può creare un certificato tramite un processo di creazione dell'insieme di credenziali delle chiavi o tramite l'importazione di un certificato esistente. Sono inclusi i certificati autofirmati e quelli generati dall'autorità di certificazione.
-   Il proprietario di un certificato dell'insieme di credenziali delle chiavi può implementare l'archiviazione sicura e la gestione di certificati X509 senza interagire con materiale della chiave privata.  
-   Il proprietario di un certificato può creare criteri che guidano l'insieme di credenziali delle chiavi nella gestione del ciclo di vita di un certificato.  
-   I proprietari di un certificato possono specificare informazioni sul contatto per notificare eventi sul ciclo di vita di un certificato, come la scadenza e il rinnovo.  
-   Viene supportato il rinnovo automatico con autorità di certificazione selezionate, ad esempio provider di certificati X509 / Autorità di certificazione partner dell'insieme di credenziali delle chiavi.

>[!Note]
>Sono ammessi anche i provider e le autorità di certificazione senza partnership, ma non supportano la funzionalità di rinnovo automatico.

## <a name="composition-of-a-certificate"></a>Componenti di un certificato

Quando viene creato un certificato Key Vault, vengono creati anche una chiave e un segreto indirizzabili con lo stesso nome. La chiave Key Vault consente operazioni di chiave e il segreto Key Vault consente il recupero del valore del certificato come segreto. Un certificato Key Vault contiene inoltre metadati del certificato x509 pubblico.  

L'identificatore e la versione dei certificati sono simili a quelli delle chiavi e segreti. Una versione specifica di una chiave e del segreto indirizzabili creati con la versione di certificato Key Vault è disponibile nella risposta di certificato Key Vault.
 
![Certificati come oggetti complessi](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Chiave esportabile o non esportabile

Un certificato di Key Vault creato può essere recuperato dal segreto indirizzabile con la chiave privata in formato PFX o PEM. I criteri usati per creare il certificato devono indicare che la chiave è esportabile. Se i criteri indicano che non è esportabile, la chiave privata non farà parte del valore recuperato come segreto.  

La chiave indirizzabile diventa maggiormente pertinente con certificati KV non esportabili. Per il mapping delle operazioni della chiave KV indirizzabile si usa il campo *keyusage* dei criteri del certificato KV usato per creare il certificato KV.  

 - Tipi di chiavi supportati: RSA, RSA-HSM, EC, EC-HSM, oct (elencati [qui](https://docs.microsoft.com/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) L'esportabilità è consentita solo con RSA, EC. Le chiavi HSM non sono esportabili.


## <a name="certificate-attributes-and-tags"></a>Tag e attributi dei certificati

Oltre ai metadati del certificato, a una chiave e a un segreto indirizzabili, un certificato di Key Vault contiene anche attributi e tag.  

### <a name="attributes"></a>Attributes

Gli attributi di certificato si riflettono negli attributi della chiave e del segreto indirizzabile creati quando viene creato KV certificato.  

Un certificato Key Vault ha gli attributi seguenti:  

-   *abilitato*: il valore predefinito booleano facoltativo è **vero**. Questo attributo può essere specificato per indicare se i dati del certificato possono essere recuperati come segreti o sono eseguibili come chiave. Viene anche usato in combinazione con *nbf* ed *exp* quando si verifica un'operazione nella finestra tra *nbf* ed *exp*. Ne sarà consentito l'uso solo se enabled è impostato su true. Le operazioni all'esterno della finestra di *nbf* ed *exp* non sono consentite automaticamente.  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta:

-   *created*: valore IntDate che indica quando è stata creata questa versione del certificato.  
-   *updated*: valore IntDate che indica quando è stata aggiornata questa versione del certificato.  
-   *exp*: valore IntDate che contiene il valore della data di scadenza del certificato x509.  
-   *nbf*: valore IntDate che contiene il valore della data del certificato x509.  

> [!Note] 
> Se un certificato in Key Vault scade, la chiave e il segreto indirizzabili sono inutilizzabili.  

### <a name="tags"></a>Tag

 Dizionario specificato dal client di coppie chiave-valore, simile ai tag delle chiavi e dei segreti.  

 > [!Note]
> I tag possono essere letti da un chiamante che ha l'autorizzazione *list* o *get* per accedere a un determinato tipo di oggetto (chiave, segreto o certificato).

## <a name="certificate-policy"></a>Criteri dei certificati

I criteri dei certificati contengono informazioni su come creare e gestire la durata di un certificato di Key Vault. Quando un certificato con chiave privata viene importato nell'insieme di credenziali delle chiavi, viene creato un criterio predefinito leggendo il certificato x509.  

Quando viene creato un certificato di Key Vault completamente nuovo, è necessario specificare i criteri. I criteri specificano come creare questa versione o la versione successiva del certificato di Key Vault. Dopo aver definito i criteri, non sarà necessario ripetere la procedura per le successive operazioni di creazione delle versioni future. È presente una sola istanza dei criteri per tutte le versioni di un certificato di Key Vault.  

In generale, i criteri dei certificati contengono le informazioni seguenti, le cui definizioni sono disponibili [qui](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy?view=azps-4.4.0):  

-   Proprietà del certificato X509: nome del soggetto, nomi alternativi del soggetto e altre proprietà usate per creare una richiesta di certificato x509.  
-   Proprietà della chiave: contiene i campi relativi al tipo di chiave, alla lunghezza della chiave, nonché all'esportabilità e il campo ReuseKeyOnRenewal. Questi campi indicare all’insieme di credenziali delle chiavi come generare una chiave. 
     - Tipi di chiavi supportati: RSA, RSA-HSM, EC, EC-HSM, oct (elencati [qui](https://docs.microsoft.com/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) 
-   Proprietà del segreto: contiene le proprietà del segreto, ad esempio tipo di contenuto del segreto indirizzabile per generare il valore del segreto, al fine di recuperare il certificato come segreto.  
-   Azioni di durata: contiene le azioni di durata per il certificato KV. Ogni azione di durata contiene:  

     - Trigger: specificato tramite i giorni precedenti alla scadenza o alla percentuale dell’intervallo di vita  

     - Azione: specificare il tipo di azione *emailContacts* o *autoRenew*  

-   Autorità di certificazione: parametri relativi all’autorità di certificazione del certificato da usare per l'emissione dei certificati x509.  
-   Attributi dei criteri: contiene gli attributi associati al criterio  

### <a name="x509-to-key-vault-usage-mapping"></a>Mappatura di X509 per l’utilizzo in Key Vault

La tabella seguente rappresenta il mapping dei criteri di utilizzo della chiave per le operazioni di chiave efficaci di una chiave creata come parte della creazione di un certificato Key Vault x509.

|**Flag utilizzo chiave X509**|**Operazioni chiave Key Vault**|**Comportamento predefinito**|
|----------|--------|--------|
|DataEncipherment|crittografa, decrittografa| N/D |
|DecipherOnly|decrypt| N/D  |
|DigitalSignature|firma, verifica| Valore predefinito Key Vault senza una specifica di utilizzo al momento della creazione di certificati | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|firma, verifica|N/D|
|KeyEncipherment|esegui/non eseguire il wrapping della chiave| Valore predefinito Key Vault senza una specifica di utilizzo al momento della creazione di certificati | 
|NonRepudiation|firma, verifica| N/D |
|crlsign|firma, verifica| N/D |

## <a name="certificate-issuer"></a>Autorità di certificazione

Un oggetto del certificato Key Vault contiene una configurazione utilizzata per comunicare con un provider dell’autorità di certificazione selezionato per ordinare certificati x509.  

-   Key Vault opera congiuntamente ai provider dell'autorità di certificazione seguente per certificati TLS/SSL

|**Nome provider**|**Percorsi**|
|----------|--------|
|DigiCert|Supportata in tutte le posizioni del servizio dell'insieme di credenziali delle chiavi nel cloud pubblico e Azure per enti pubblici|
|GlobalSign|Supportata in tutte le posizioni del servizio dell'insieme di credenziali delle chiavi nel cloud pubblico e Azure per enti pubblici|

Prima della creazione di un'autorità di certificazione in Key Vault, è necessario eseguire i seguenti passaggi prerequisiti 1 e 2.  

1. Eseguire l’onboarding ai provider dell’autorità di certificazione (CA)  

    -   Un amministratore dell'organizzazione deve eseguire l’onboardig della propria azienda (es. Contoso) con almeno un provider di autorità di certificazione.  

2. L'amministratore crea le credenziali del richiedente per Key Vault al fine di registrare (e rinnovare) i certificati TLS/SSL  

    -   Fornisce la configurazione da utilizzare per creare un oggetto dell'autorità di certificazione del provider nell'insieme di credenziali delle chiavi  

Per ulteriori informazioni sulla creazione di oggetti di autorità di certificazione dal portale di certificati, vedere il [blog dei certificati Key Vault](https://aka.ms/kvcertsblog)  

Key Vault consente di creare più oggetti di autorità di certificazione con una differente configurazione del provider dell'autorità di certificazione. Dopo che è stato creato un oggetto di autorità di certificazione, è possibile fare riferimento al suo nome in uno o più criteri del certificato KV. Fare riferimento all'oggetto dell'autorità di certificazione indica a Key Vault di usare la configurazione come specificato nell'oggetto dell'autorità di certificazione quando si richiede il certificato x509 dal provider di autorità di certificazione durante la creazione e il rinnovo di certificati.  

Gli oggetti di autorità di certificazione vengono creati nell'insieme di credenziali e possono essere utilizzati solo con certificati KV nello stesso insieme di credenziali.  

## <a name="certificate-contacts"></a>Contatti relativi al certificato

I contatti relativi al certificato contengono le informazioni di contatto per inviare notifiche attivate da eventi di durata dei certificati. Le informazioni dei contatti vengono condivise da tutti i certificati nell'insieme di credenziali delle chiavi. Viene inviata una notifica a tutti i contatti specificati per un evento per qualsiasi certificato presente nell'insieme di credenziali delle chiavi.  

Se i criteri del certificato sono impostati per il rinnovo automatico, viene inviata una notifica sugli eventi seguenti.  

- Prima del rinnovo del certificato
- Dopo il rinnovo del certificato, che indica se il certificato è stato rinnovato, o se si è verificato un errore, richiede il rinnovo manuale del certificato.  

  Se i criteri del certificato sono impostati per il rinnovo manuale (solo posta elettronica), viene inviata una notifica al momento del rinnovo.  

## <a name="certificate-access-control"></a>Controllo di accesso per i certificati

 Il controllo di accesso per i certificati è gestito da Key Vault e viene fornito dall'insieme di credenziali delle chiavi che contiene tali certificati. I criteri di controllo di accesso per i certificati sono distinti dai criteri di controllo di accesso per le chiavi e i segreti presenti nello stesso insieme di credenziali delle chiavi. Gli utenti possono creare uno o più insiemi di credenziali per i certificati e per mantenere una segmentazione e una gestione dei certificati appropriate in base allo scenario.  

 Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali delle chiavi e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

- Autorizzazioni per le operazioni di gestione dei certificati
  - *get*: consente di ottenere la versione corrente del certificato o qualsiasi versione di un certificato 
  - *list*: consente di elencare i certificati correnti o le versioni di un certificato  
  - *update*: consente di aggiornare un certificato
  - *create*: consente di creare un certificato di Key Vault
  - *import*: consente di importare il materiale apposito in un certificato di Key Vault
  - *delete*: consente di eliminare un certificato, nonché i relativi criteri e versioni  
  - *recover*: consente di recuperare un certificato eliminato
  - *backup*: consente di eseguire il backup di un certificato in un insieme di credenziali delle chiavi
  - *restore*: consente di ripristinare un certificato sottoposto a backup in un insieme di credenziali delle chiavi
  - *managecontacts*: consente di gestire i contatti del certificato in un Key Vault  
  - *manageissuers*: consente di gestire autorità di certificazione di Key Vault
  - *getissuers*: consente di ottenere le autorità emittenti del certificato
  - *listissuers*: consente di elencare le autorità emittenti del certificato  
  - *setissuers*: consente di creare o aggiornare le autorità di certificazione di un certificato di Key Vault  
  - *deleteissuers*: consente di eliminare le autorità di certificazione del certificato di Key Vault  
 
- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente un certificato

Per altre informazioni, vedere le [operazioni relative ai certificati nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Key Vault](../general/overview.md)
- [Informazioni su chiavi, segreti e certificati](../general/about-keys-secrets-certificates.md)
- [Informazioni sulle chiavi](../keys/about-keys.md)
- [Informazioni sui segreti](../secrets/about-secrets.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
