---
title: Connettività SSL/TLS-database di Azure per MySQL
description: Informazioni per la configurazione del Database di Azure per MySQL e delle applicazioni associate per usare correttamente le connessioni SSL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 6fb0b6f38869192e89bb849950e8822a157b23c8
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241940"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Connettività SSL/TLS nel database di Azure per MySQL

Il Database di Azure per MySQL supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

> [!NOTE]
> L'aggiornamento del `require_secure_transport` valore del parametro Server non influisce sul comportamento del servizio MySQL. Usare le funzionalità di imposizione SSL e TLS descritte in questo articolo per proteggere le connessioni al database.

>[!NOTE]
> In base ai commenti e suggerimenti dei clienti abbiamo esteso la deprecazione del certificato radice per la CA radice Baltimore esistente fino al 15 febbraio 2021 (02/15/2021).

> [!IMPORTANT] 
> Il certificato radice SSL è impostato per scadere a partire dal 15 febbraio 2021 (02/15/2021). Aggiornare l'applicazione per usare il [nuovo certificato](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Per altre informazioni, vedere [aggiornamenti di certificati pianificati](concepts-certificate-rotation.md)

## <a name="ssl-default-settings"></a>Impostazioni predefinite SSL

Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MySQL.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile.

Per impostazione predefinita, l'opzione di applicazione delle connessioni SSL è attiva quando si esegue il provisioning di un nuovo Database di Azure per il server MySQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni** , selezionare **Stringhe di connessione** . Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

In alcuni casi, le applicazioni richiedono un file di certificato locale generato da un file di certificato dell'autorità di certificazione (CA) attendibile per la connessione sicura. Attualmente i clienti possono **usare solo** il certificato predefinito per connettersi a un database di Azure per il server MySQL, disponibile in https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Analogamente, i collegamenti seguenti puntano ai certificati per i server nei cloud sovrani: [Azure per enti pubblici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Cina](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Germania](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Per informazioni su come abilitare o disabilitare la connessione SSL durante lo sviluppo dell'applicazione, consultare [Come configurare SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Imposizione di TLS nel database di Azure per MySQL

Database di Azure per MySQL supporta la crittografia per i client che si connettono al server di database usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete sicure tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

### <a name="tls-settings"></a>Impostazioni di TLS

Database di Azure per MySQL offre la possibilità di applicare la versione TLS per le connessioni client. Per applicare la versione TLS, usare l'impostazione dell'opzione **versione minima di TLS** . Per questa impostazione di opzione sono consentiti i valori seguenti:

|  Impostazione minima di TLS             | Versione TLS del client supportata                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (impostazione predefinita) | Non sono necessari TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e versioni successive           |
| TLS1_1                           | TLS 1,1, TLS 1,2 e versioni successive                   |
| TLS1_2                           | TLS versione 1,2 e successive                     |


Se ad esempio si imposta il valore della versione minima dell'impostazione TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, l'impostazione di questa opzione su 1,2 significa che è possibile consentire solo le connessioni dai client che usano TLS 1.2 + e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

> [!Note] 
> Per impostazione predefinita, database di Azure per MySQL non impone una versione minima di TLS (impostazione `TLSEnforcementDisabled` ).
>
> Quando si applica una versione minima di TLS, non sarà più possibile disabilitare l'imposizione minima della versione.

Per informazioni su come impostare l'impostazione TLS per il database di Azure per MySQL, vedere [How to configure TLS setting](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mysql-single-server"></a>Supporto crittografico da database di Azure per server singolo MySQL

Come parte della comunicazione SSL/TLS, i pacchetti di crittografia sono convalidati e supportano solo i pacchetti di crittografia che possono comunicare con il database server. La convalida del pacchetto di crittografia viene controllata nel [livello gateway](concepts-connectivity-architecture.md#connectivity-architecture) e non in modo esplicito nel nodo stesso. Se i pacchetti di crittografia non corrispondono a uno dei gruppi elencati di seguito, le connessioni client in ingresso verranno rifiutate.

### <a name="cipher-suite-supported"></a>Pacchetto di crittografia supportato

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Passaggi successivi

- [Librerie di connessione per database di Azure per MySQL](concepts-connection-libraries.md)
- Informazioni su come [configurare SSL](howto-configure-ssl.md)
- Informazioni su come [configurare TLS](howto-tls-configurations.md)
