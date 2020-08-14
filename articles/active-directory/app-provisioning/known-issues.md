---
title: Problemi noti per il provisioning dell'applicazione in Azure AD
description: Informazioni sui problemi noti quando si lavora con il provisioning automatizzato delle applicazioni in Azure AD.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/12/2020
ms.reviewer: arvinh
ms.openlocfilehash: 127629cb0102c2736995364db9202cd837d99a17
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214281"
---
# <a name="known-issues-application-provisioning"></a>Problemi noti: provisioning delle applicazioni
Problemi noti da tenere presente quando si utilizza il provisioning delle app. È possibile fornire commenti e suggerimenti sul servizio di provisioning dell'applicazione in UserVoice, vedere Azure AD provisioning [dell'applicazione UserVoice](https://aka.ms/appprovisioningfeaturerequest). Osserviamo attentamente UserVoice per poter migliorare il servizio. 

> [!NOTE]
> Questo non è un elenco completo di problemi noti. Se si è a conoscenza di un problema non elencato, fornire commenti e suggerimenti nella parte inferiore della pagina.

## <a name="authorization"></a>Autorizzazione 

**Non è possibile salvare dopo il test della connessione riuscito**

Se è possibile testare correttamente una connessione, ma non è possibile salvare, è stato superato il limite di archiviazione consentito per le credenziali. Per altre informazioni, vedere [problemi di salvataggio delle credenziali di amministratore](application-provisioning-config-problem-storage-limit.md).

**Non è possibile salvare**

Per il salvataggio è necessario specificare l'URL del tenant, il token segreto e il messaggio di posta elettronica di notifica. Non è possibile fornire solo uno di essi. 

**Impossibile modificare la modalità di provisioning manuale**

Dopo aver configurato il provisioning per la prima volta, si noterà che la modalità di provisioning è cambiata da manuale a automatico. Non è possibile modificarlo di nuovo in manuale. Tuttavia, è possibile disattivare il provisioning tramite l'interfaccia utente. La disattivazione del provisioning nell'interfaccia utente funziona in modo efficace come l'impostazione dell'elenco a discesa su manuale.  


## <a name="attribute-mappings"></a>Mapping degli attributi 

**Attributo SamAccountName o userType non disponibile come attributo di origine**

Per impostazione predefinita, gli attributi SamAccountName e userType non sono disponibili come attributo di origine. Estendere lo schema per aggiungere l'attributo. È possibile aggiungere gli attributi all'elenco di attributi di origine disponibili estendendo lo schema. Per altre informazioni, vedere [attributo Source mancante](user-provisioning-sync-attributes-for-mapping.md). 

**Elenco a discesa attributo di origine mancante per l'estensione dello schema**

Le estensioni dello schema possono a volte mancare dall'elenco a discesa dell'attributo di origine nell'interfaccia utente. Passare alle impostazioni avanzate dei mapping degli attributi e aggiungere manualmente gli attributi. Per altre informazioni, vedere [personalizzare i mapping degli attributi](customize-application-attributes.md).

**Non è possibile eseguire il provisioning dell'attributo null**

Attualmente non è possibile eseguire il provisioning di attributi NULL Azure AD Se un attributo è null nell'oggetto utente, verrà ignorato. 

**Numero massimo di caratteri per le espressioni di mapping degli attributi**

Le espressioni di mapping degli attributi possono avere un massimo di 10.000 caratteri. 


## <a name="service-issues"></a>Problemi relativi al servizio 

**Scenari non supportati**

- Il provisioning delle password non è supportato. 
- Il provisioning di gruppi annidati non è supportato. 
- Il provisioning in tenant B2C non è supportato a causa delle dimensioni dei tenant. 

**Le modifiche non passano dall'app di destinazione al Azure AD**

Il servizio di provisioning delle app non è in grado di riconoscere le modifiche apportate alle app esterne. Non viene quindi eseguita alcuna azione per eseguire il rollback. Il servizio di provisioning delle app si basa sulle modifiche apportate in Azure AD.  

**Il ciclo di provisioning continua fino al completamento**

Quando si imposta il provisioning `enabled = off` o si preme Stop, il ciclo di provisioning corrente continuerà a essere eseguito fino al completamento. Il servizio smetterà di eseguire i cicli futuri fino a quando non si attiva di nuovo il provisioning.

**Membro del gruppo senza provisioning**

Quando un gruppo è nell'ambito e un membro è esterno all'ambito, viene eseguito il provisioning del gruppo. Non verrà eseguito il provisioning dell'utente esterno all'ambito. Se il membro torna all'ambito, il servizio non rileverà immediatamente la modifica. Il riavvio del provisioning risolverà il problema. È consigliabile riavviare periodicamente il servizio per assicurarsi che venga effettuato correttamente il provisioning di tutti gli utenti.  


## <a name="next-steps"></a>Passaggi successivi
- [Come funziona il provisioning](how-provisioning-works.md)
