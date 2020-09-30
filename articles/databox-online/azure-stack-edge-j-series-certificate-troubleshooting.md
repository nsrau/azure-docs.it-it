---
title: Risoluzione dei problemi relativi ai certificati con Azure Stack Edge Pro con GPU | Microsoft Docs
description: Descrive la risoluzione degli errori relativi ai certificati con Azure Stack dispositivo GPU Pro Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: f0f9dfa6e3d6ae02f66ac71f62586953cb21517e
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568353"
---
# <a name="troubleshooting-certificate-errors"></a>Risoluzione degli errori relativi ai certificati

Questo articolo fornisce la risoluzione degli errori comuni relativi ai certificati durante l'installazione di certificati nel dispositivo Azure Stack Edge Pro.

## <a name="common-certificate-errors"></a>Errori comuni relativi ai certificati

La tabella seguente illustra gli errori comuni relativi ai certificati e le informazioni dettagliate su questi errori e sulle possibili soluzioni:

> [!NOTE]
> Le occorrenze di &#8220;{0} , {1} ,..., {n} &#8221; indicano parametri posizionali. I parametri posizionali prenderanno i valori a seconda dei certificati utilizzati.

| Codice di errore | Descrizione |
|---|---|
| CertificateManagement_UntrustedCertificate | La catena di certificati del certificato con nome soggetto {0} è interruppe. Caricare il certificato della catena di firma prima di caricare il certificato.|
| CertificateManagement_DeviceNotRegistered| Il dispositivo non è attivato. È possibile caricare un certificato di supporto solo dopo l'attivazione.|
| CertificateManagement_ExpiredCertificate | Il certificato con tipo {0} è scaduto o scade a breve. Controllare la scadenza del certificato e, se necessario, importare un nuovo certificato.|
| CertificateManagement_FormatMismatch | Il formato del certificato non è supportato. Verificare il formato del certificato e, se necessario, importare un nuovo certificato.  Previsto {0} , trovato {1} . |
| CertificateManagement_GenericError | Non è stato possibile eseguire l'operazione di gestione dei certificati. Ripetere l'operazione tra qualche minuto. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| CertificateManagement_HttpsBindingFailure | Il certificato con nome soggetto {0} non è riuscito a creare un canale HTTPS sicuro. Controllare il certificato caricato e, se necessario, importare un nuovo certificato. Questo errore si verifica con il certificato del nodo del dispositivo.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Il certificato con nome soggetto {0} non deve avere la firma del certificato di utilizzo chiave. Controllare l'utilizzo della chiave del certificato e, se necessario, importare un nuovo certificato. Questo errore si verifica con il certificato della catena di firma. |
| CertificateManagement_IncorrectKeyNumber | Il numero di chiave del certificato con nome soggetto non {0} è corretto {1} . Verificare il numero di chiave del certificato e, se necessario, importare un nuovo certificato.|
| CertificateManagement_InvalidP7b | Il file di certificato caricato non è valido.  Verificare il formato del certificato e, se necessario, importare un nuovo certificato.|
| CertificateManagement_KeyAlgorithmNotRSA | Questo certificato non usa l'algoritmo della chiave RSA. Sono supportati solo i certificati RSA. |
| CertificateManagement_KeySizeNotSufficient | La dimensione della chiave del certificato con nome soggetto non {0} è sufficiente {1} . La dimensione minima della chiave è 4096.|
| CertificateManagement_MissingClientOid | Il certificato con nome soggetto non {0} dispone dell'OID di autenticazione client. Controllare le proprietà del certificato e, se necessario, importare un nuovo certificato.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | Il certificato con nome soggetto {0} non ha una firma digitale nell'utilizzo della chiave. Controllare le proprietà del certificato e, se necessario, importare un nuovo certificato. |
| CertificateManagement_MissingKeyCertSignKeyUsage | Il certificato con nome soggetto {0} non dispone dell'accesso alla chiave. Controllare le proprietà del certificato e, se necessario, importare un nuovo certificato.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | Il certificato con nome soggetto {0} non ha la crittografia chiave nell'utilizzo della chiave. Controllare le proprietà del certificato e, se necessario, importare un nuovo certificato. |
| CertificateManagement_MissingServerOid | Il certificato con nome soggetto non {0} dispone dell'OID di autenticazione server. Controllare le proprietà del certificato e, se necessario, importare un nuovo certificato.|
| CertificateManagement_NameMismatch | Tipo di certificato non corrispondente. Ambito previsto: {0} , trovato {1} . Caricare il certificato appropriato.|
| CertificateManagement_NoPrivateKeyPresent | Per il certificato con nome soggetto {0} non è presente alcuna chiave privata. Caricare un certificato con estensione pfx con chiave privata.|
| CertificateManagement_NotSelfSignedCertificate | Il certificato con nome soggetto {0} non è firmato autonomamente. I certificati radice devono essere autofirmati |
| CertificateManagement_NotSupportedOnVirtualAppliance | Questa operazione non è supportata nel dispositivo virtuale. Questo errore indica che la firma si verificherà solo con Data Box Gateway in esecuzione nell'appliance cloud tattica. Questo errore si verifica durante la gestione del dispositivo tramite Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | Il certificato con nome soggetto {0} è autofirmato. Caricare un certificato firmato correttamente.|
| CertificateManagement_SignatureAlgorithmSha1 | L'algoritmo di firma del certificato con nome soggetto non {0} è supportato. SHA1-RSA non è supportato. |
| CertificateManagement_SubjectNamesInvalid | Il certificato con nome soggetto {0} non ha il nome soggetto o i nomi alternativi del soggetto corretti per il {1} certificato. Controllare il certificato caricato e, se necessario, importare un nuovo certificato. Verificare anche che il nome DNS corrisponda ai nomi di San.|
| CertificateManagement_UnreadableCertificate | Impossibile leggere il certificato con tipo {0} . Questo errore si verifica quando il certificato è illeggibile o è danneggiato. Importare un nuovo certificato.|
| CertificateSubjectNotFound | Impossibile trovare il certificato con nome soggetto {0} . Importare un nuovo certificato.|
| CertificateRotationGenericFailure | La rotazione di uno o più certificati non è riuscita. Riprovare tra qualche minuto. Se il problema persiste, contattare il supporto tecnico Microsoft.|
| CertificateImportFailure | Il certificato con identificazione personale {0} non è stato importato nel nodo {1} . Se il problema persiste, contattare il supporto tecnico Microsoft. |
| CertificateApplyFailure | Il certificato con identificazione personale {0} non è stato applicato al nodo {1} . Se il problema persiste, contattare il supporto tecnico Microsoft.|
| NodeNotReachable | Non è stato possibile convalidare il certificato {0} . Verificare l'integrità del software e dell'hardware del sistema.|

## <a name="next-steps"></a>Passaggi successivi

[Requisiti del certificato](azure-stack-edge-j-series-certificate-requirements.md)