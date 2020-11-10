---
title: Modifiche ai certificati TLS di Azure
description: Modifiche ai certificati TLS di Azure
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: dbc426144678f5bd7382b0961bf9bc3f5339b97a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409808"
---
# <a name="azure-tls-certificate-changes"></a>Modifiche ai certificati TLS di Azure  

Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Questa modifica viene apportata perché i certificati della CA corrente non sono conformi a uno dei requisiti correnti di CA/Browser Forum Baseline.

## <a name="when-will-this-change-happen"></a>Quando verrà applicata questa modifica?

- La transizione dei servizi di [Azure Active Directory](../../active-directory/index.yml) (Azure AD) inizia il 7 luglio 2020.
- Tutti gli endpoint TLS/SSL di Azure appena creati contengono certificati aggiornati concatenati alle nuove CA radice.
- Per gli endpoint di Azure esistenti verrà avviata una transizione in fasi a partire dal 13 agosto 2020.
- L'[hub IoT di Azure](https://azure.microsoft.com/services/iot-hub) e il [servizio Device Provisioning](../../iot-dps/index.yml) rimarranno di competenza della CA Baltimore CyberTrust Root, ma le relative CA intermedie cambieranno. [Fare clic qui per i dettagli](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- I servizio [Archiviazione di Azure](../../storage/index.yml) rimarrà di competenza della CA Baltimore CyberTrust Root, ma le relative CA intermedie cambieranno. [Fare clic qui per i dettagli](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Cache di Azure per Redis](../../azure-cache-for-redis/index.yml) rimarrà nell'autorità di certificazione radice Baltimore Cybertrust, ma le autorità di certificazione intermedie cambieranno. [Fare clic qui per i dettagli](../../azure-cache-for-redis/cache-whats-new.md).
> [!IMPORTANT]
> Dopo questa modifica, è possibile che i clienti debbano aggiornare le loro applicazioni per evitare problemi di connettività quando provano a connettersi ai servizi di Azure.

## <a name="what-is-changing"></a>Cosa cambierà

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
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Quando è possibile ritirare le identificazioni personali intermedie precedenti?

I certificati della CA corrente *non* verranno revocati fino al 15 febbraio 2021. Dopo tale data è possibile rimuovere le identificazioni personali precedenti dal codice.

Se questa data cambia, si riceverà un avviso sulla nuova data di revoca.

## <a name="will-this-change-affect-me"></a>Quali sono gli effetti di questa modifica? 

Si prevede che la modifica **non influirà sulla maggior parte dei clienti di Azure non**.  Tuttavia, le applicazioni potrebbero essere interessate se specificano esplicitamente un elenco di CA accettabili. Questa procedura è nota come associazione di certificati.

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
    - http://www&#46; Microsoft&#46;com/pkiops

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori domande, contattare il [supporto tecnico](https://azure.microsoft.com/support/options/).