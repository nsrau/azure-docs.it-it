---
title: Novità di Azure Key Vault
description: Aggiornamenti recenti per Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: fac5fad51137cd08f2498db132768263a770430d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203875"
---
# <a name="whats-new-for-azure-key-vault"></a>Novità di Azure Key Vault

Ecco le novità di Azure Key Vault. Le nuove funzionalità e i miglioramenti vengono annunciati anche sul [canale di Key Vault di aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="july-2020"></a>Luglio 2020

> [!WARNING]
> Questi due aggiornamenti possono avere effetti sulle implementazioni di Azure Key Vault.

### <a name="soft-delete-on-by-default"></a>Eliminazione temporanea attivata per impostazione predefinita

Entro la fine del 2020, **l'eliminazione temporanea sarà attivata per impostazione predefinita per tutti gli insiemi di credenziali delle chiavi** , sia nuovi che preesistenti. Per informazioni dettagliate su questa modifica di rilievo, oltre che sulla procedura per trovare gli insiemi di credenziali delle chiavi interessati e aggiornarli in anticipo, vedere l'articolo[L'eliminazione temporanea verrà abilitata in tutti gli insiemi di credenziali delle chiavi](soft-delete-change.md). 

### <a name="azure-tls-certificate-changes"></a>Modifiche ai certificati TLS di Azure  

Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Questa modifica viene apportata perché i certificati della CA corrente [non sono conformi a uno dei requisiti correnti di CA/Browser Forum Baseline](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951).

### <a name="when-will-this-change-happen"></a>Quando verrà applicata questa modifica?

- La transizione dei servizi di [Azure Active Directory](/azure/active-directory) (Azure AD) inizia il 7 luglio 2020.
- Tutti gli endpoint TLS/SSL di Azure appena creati contengono certificati aggiornati concatenati alle nuove CA radice.
- Per gli endpoint di Azure esistenti verrà avviata una transizione in fasi a partire dal 13 agosto 2020.
- L'[hub IoT di Azure](https://azure.microsoft.com/services/iot-hub) e il [servizio Device Provisioning](/azure/iot-dps/) rimarranno di competenza della CA Baltimore CyberTrust Root, ma le relative CA intermedie cambieranno. Per i dettagli completi, vedere il post di blog [Azure IoT TLS: Changes are coming! (…and why you care)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- I servizio [Archiviazione di Azure](/azure/storage) rimarrà di competenza della CA Baltimore CyberTrust Root, ma le relative CA intermedie cambieranno. Per i dettagli completi, vedere il post di blog [Azure Storage TLS: Changes are coming! (…and why you care)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).

> [!IMPORTANT]
> Dopo questa modifica, è possibile che i clienti debbano aggiornare le loro applicazioni per evitare problemi di connettività quando provano a connettersi ai servizi di Azure.

### <a name="what-is-changing"></a>Cosa cambierà

Attualmente, la maggior parte dei certificati TLS usati dai servizi di Azure sono concatenati alla CA radice seguente:

| Nome comune della CA | Thumbprint (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

I certificati TLS usati dai servizi di Azure verranno concatenati a una delle CA radice seguenti:

| Nome comune della CA | Thumbprint (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Quando è possibile ritirare le identificazioni personali intermedie precedenti?

I certificati della CA corrente *non* verranno revocati fino al 15 febbraio 2021. Dopo tale data è possibile rimuovere le identificazioni personali precedenti dal codice.

Se questa data cambia, si riceverà un avviso sulla nuova data di revoca.

### <a name="will-this-change-affect-me"></a>Quali sono gli effetti di questa modifica? 

Si prevede che la modifica **non influirà sulla maggior parte dei clienti di Azure non** .  Tuttavia, le applicazioni potrebbero essere interessate se specificano esplicitamente un elenco di CA accettabili. Questa procedura è nota come associazione di certificati.

Ecco come rilevare se un'applicazione è interessata:

- Cercare nel codice sorgente l'identificazione personale, il nome comune e altre proprietà del certificato di una delle CA TLS di Microsoft IT riportate [qui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Se si trova una corrispondenza, l'applicazione sarà interessata. Per risolvere il problema, aggiornare il codice sorgente per includere le nuove CA. Come procedura consigliata, assicurarsi che le CA possano essere aggiunte o modificate con un breve preavviso. Le normative di settore richiedono la sostituzione dei certificati delle CA entro sette giorni, quindi i clienti che si basano sull'associazione dovranno reagire rapidamente.

- Se un'applicazione si integra con API di Azure o con altri servizi di Azure e non si sa se usa l'associazione di certificati, contattare il fornitore dell'applicazione.

- Per sistemi operativi e runtime di linguaggi diversi che comunicano con i servizi di Azure può essere necessario eseguire passaggi aggiuntivi per creare correttamente la catena di certificati con queste nuove radici:
    - **Linux** : per molte distribuzioni è necessario aggiungere le CA e in /etc/ssl/certs. Per istruzioni specifiche, fare riferimento alla documentazione della distribuzione.
    - **Java** : verificare che l'archivio chiavi Java contenga le CA elencate sopra.
    - **Windows in esecuzione in ambienti disconnessi** : per i sistemi in esecuzione in ambienti disconnessi sarà necessario aggiungere le nuove radici all'archivio di autorità di certificazione radice attendibili e le CA intermedie all'archivio di autorità di certificazione intermedie.
    - **Android** : controllare la documentazione relativa al dispositivo e alla versione di Android.
    - **Altri dispositivi hardware, soprattutto IoT** : contattare il produttore del dispositivo.

- Se l'ambiente include regole del firewall impostate per consentire le chiamate in uscita solo a specifiche posizioni CRL (Certificate Revocation List) e/o OCSP (Online Certificate Status Protocol), sarà necessario autorizzare gli URL CRL e OCSP seguenti:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>Giugno 2020

Monitoraggio di Azure per Key Vault è ora disponibile in anteprima.  Monitoraggio di Azure offre il monitoraggio completo degli insiemi di credenziali delle chiavi mettendo a disposizione una vista unificata di richieste, prestazioni, errori e latenza di Key Vault. Per altre informazioni, vedere [Monitoraggio di Azure per Key Vault (anteprima)](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maggio 2020

Il modello BYOK (Bring Your Own Key) di Key Vault è ora disponibile a livello generale. Vedere la [specifica BYOK di Azure Key Vault](../keys/byok-specification.md) e l'articolo [Importare chiavi con protezione HSM in Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Marzo 2020

Gli endpoint privati sono ora disponibili in anteprima. Il servizio Collegamento privato di Azure consente di accedere ad Azure Key Vault e ai servizi di clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.  Vedere come [integrare Key Vault con Collegamento privato di Azure](private-link-service.md).

## <a name="2019"></a>2019

- Rilascio della nuova generazione di Azure Key Vault SDK. Per esempi relativi all'uso, vedere gli argomenti di avvio rapido sui segreti di Azure Key Vault per [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) e [Node.js](../secrets/quick-create-node.md)
- Nuovi criteri di Azure per gestire i certificati degli insiemi di credenziali delle chiavi. Vedere [Definizioni predefinite di Criteri di Azure per Key Vault](../policy-samples.md).
- Estensione macchina virtuale di Azure Key Vault ora disponibile a livello generale.  Vedere [Estensione macchina virtuale di Key Vault per Linux](../../virtual-machines/extensions/key-vault-linux.md) e [Estensione macchina virtuale di Key Vault per Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Gestione dei segreti basati su eventi per Azure Key Vault ora disponibile in Griglia di eventi di Azure. Per altre informazioni, vedere [lo schema di Griglia di eventi per eventi in Azure Key Vault](../../event-grid/event-schema-key-vault.md] e l'articolo [Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nuove funzionalità e integrazioni rilasciate quest'anno:

- Integrazione con Funzioni di Azure. Per uno scenario di esempio che sfrutta [Funzioni di Azure](../../azure-functions/index.yml) per le operazioni dell'insieme di credenziali delle chiavi, vedere [Automatizzare la rotazione di un segreto](../secrets/tutorial-rotation.md). 
- [Integrazione con Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Con questa integrazione, Azure Databricks supporta ora due tipi di ambiti dei segreti, con supporto di Azure Key Vault e con supporto di Databricks. Per altre informazioni, vedere [Creare un ambito dei segreti con supporto di Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Endpoint di servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nuove funzionalità rilasciate quest'anno:

- Chiavi gestite di account di archiviazione. La funzionalità delle chiavi di account di archiviazione si integra ancora più facilmente con Archiviazione di Azure. Per altre informazioni, vedere l'argomento introduttivo [Chiavi dell'account di archiviazione Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Eliminazione temporanea. La funzionalità di eliminazione temporanea migliora la protezione dei dati degli insiemi di credenziali delle chiavi e dei relativi oggetti. Per altre informazioni, vedere l'argomento introduttivo [Panoramica di eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nuove funzionalità rilasciate quest'anno:
- Gestione dei certificati. Aggiunta come funzionalità alla versione 2015-06-01 in disponibilità generale il 26 settembre 2016.

La disponibilità generale (versione 2015-06-01) è stata annunciata il 24 giugno 2015. In questa versione sono state apportate le modifiche seguenti: 
- Eliminazione di una chiave: campo "use" rimosso.
- Recupero di informazioni relative a una chiave: campo "use" rimosso.
- Importazione di una chiave in un insieme di credenziali: campo "use" rimosso.
- Ripristino di una chiave: campo "use" rimosso.     
- Sostituzione di "RSA_OAEP" con "RSA-OAEP" per gli algoritmi RSA. Vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).    
 
La seconda versione di anteprima (2015-02-01-preview) è stata annunciata il 20 aprile 2015. Per altre informazioni, vedere il post di blog [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) (Aggiornamento API REST). Sono state aggiornate le attività seguenti:
 
- Elencare le chiavi in un insieme di credenziali - aggiunta del supporto per la paginazione all'operazione.
- Elencare le versioni di una chiave - aggiunta dell'operazione per elencare le versioni di una chiave.  
- Elencare i segreti in un insieme di credenziali - aggiunta del supporto per la paginazione.
- Elencare le versioni di un segreto - aggiunta di un'operazione per elencare le versioni di un segreto.  
- Tutte le operazioni - aggiunta del timestamp di creazione/aggiornamento agli attributi.  
- Creare un segreto - aggiunta di Content-Type ai segreti.
- Creare una chiave - aggiunta di tag come informazioni facoltative.
- Creare un segreto - aggiunta di tag come informazioni facoltative.
- Aggiornare una chiave - aggiunta di tag come informazioni facoltative.
- Aggiornare un segreto - aggiunta di tag come informazioni facoltative.
- Modifica delle dimensioni massime dei segreti da 10 K a 25 kB. Vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
La prima versione di anteprima (2014-12-08-preview) è stata annunciata l'8 gennaio 2015.  

## <a name="next-steps"></a>Passaggi successivi

Per eventuali altre domande, contattare il [supporto](https://azure.microsoft.com/support/options/).  
