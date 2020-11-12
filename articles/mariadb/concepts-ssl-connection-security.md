---
title: Connettività SSL/TLS-database di Azure per MariaDB
description: Informazioni per la configurazione di Database di Azure per MariaDB e delle applicazioni associate per usare correttamente le connessioni SSL
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: d2d916f3cba27f6b38a781b81e403ee9b6f0fab3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541046"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>Connettività SSL/TLS nel database di Azure per MariaDB
Database di Azure per MariaDB supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

>[!NOTE]
> In base ai commenti e suggerimenti dei clienti abbiamo esteso la deprecazione del certificato radice per la CA radice Baltimore esistente fino al 15 febbraio 2021 (02/15/2021).

> [!IMPORTANT] 
> Il certificato radice SSL è impostato per scadere a partire dal 15 febbraio 2021 (02/15/2021). Aggiornare l'applicazione per usare il [nuovo certificato](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Per altre informazioni, vedere [aggiornamenti di certificati pianificati](concepts-certificate-rotation.md)

## <a name="default-settings"></a>Impostazioni predefinite
Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MariaDB.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile.

Quando si effettua il provisioning di un nuovo server Database di Azure per MariaDB attraverso il portale di Azure e l'interfaccia della riga di comando, vengono abilitate per impostazione predefinita le connessioni SSL.

In alcuni casi, le applicazioni richiedono un file di certificato locale generato da un file di certificato dell'autorità di certificazione (CA) attendibile per la connessione sicura. Attualmente i clienti possono **usare solo** il certificato predefinito per connettersi a un database di Azure per il server MariaDB che si trova in https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Analogamente, i collegamenti seguenti puntano ai certificati per i server nei cloud sovrani: [Azure per enti pubblici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Cina](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Germania](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni** , selezionare **Stringhe di connessione**. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

Per informazioni su come abilitare o disabilitare la connessione SSL durante lo sviluppo dell'applicazione, consultare [Come configurare SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Imposizione di TLS nel database di Azure per MariaDB

Il database di Azure per MariaDB supporta la crittografia per i client che si connettono al server di database usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete sicure tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

### <a name="tls-settings"></a>Impostazioni di TLS

Il database di Azure per MariaDB offre la possibilità di applicare la versione TLS per le connessioni client. Per applicare la versione TLS, usare l'impostazione dell'opzione **versione minima di TLS** . Per questa impostazione di opzione sono consentiti i valori seguenti:

|  Impostazione minima di TLS             | Versione TLS del client supportata                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (impostazione predefinita) | Non sono necessari TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e versioni successive         |
| TLS1_1                           | TLS 1,1, TLS 1,2 e versioni successive              |
| TLS1_2                           | TLS versione 1,2 e successive                  |


Se ad esempio si imposta il valore della versione minima dell'impostazione TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, l'impostazione di questa opzione su 1,2 significa che è possibile consentire solo le connessioni dai client che usano TLS 1.2 + e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

> [!Note] 
> Per impostazione predefinita, il database di Azure per MariaDB non impone una versione minima di TLS (impostazione `TLSEnforcementDisabled` ).
>
> Quando si applica una versione minima di TLS, non sarà più possibile disabilitare l'imposizione minima della versione.

Per informazioni su come impostare l'impostazione TLS per il database di Azure per MariaDB, vedere [How to configure TLS setting](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mariadb"></a>Supporto di crittografia da database di Azure per MariaDB

Come parte della comunicazione SSL/TLS, i pacchetti di crittografia sono convalidati e supportano solo i pacchetti di crittografia che possono comunicare con il database server. La convalida del pacchetto di crittografia viene controllata nel [livello gateway](concepts-connectivity-architecture.md#connectivity-architecture) e non in modo esplicito nel nodo stesso. Se i pacchetti di crittografia non corrispondono a uno dei gruppi elencati di seguito, le connessioni client in ingresso verranno rifiutate.

### <a name="cipher-suite-supported"></a>Pacchetto di crittografia supportato

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [regole del firewall del server](concepts-firewall-rules.md)
- Informazioni su come [configurare SSL](howto-configure-ssl.md)
- Informazioni su come [configurare TLS](howto-tls-configurations.md)
