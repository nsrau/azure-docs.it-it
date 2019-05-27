---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0334f9fd2d749b88580ff3857d705de2ae961902
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157741"
---
Il gateway di rete virtuale usa una subnet specifica denominata subnet del gateway. La subnet del gateway è inclusa nell'intervallo di indirizzi IP della rete virtuale specificato durante la configurazione della rete virtuale. Contiene gli indirizzi IP usati dai servizi e dalle risorse del gateway di rete virtuale. Affinché Azure vi distribuisca le risorse gateway, la subnet deve essere denominata "GatewaySubnet". Non è possibile specificare una diversa subnet per la distribuzione delle risorse gateway. Se non è presente una subnet denominata "GatewaySubnet" quando si crea il gateway VPN, l'operazione avrà esito negativo.

Quando si crea la subnet del gateway, si specifica il numero di indirizzi IP inclusi nella subnet. Il numero di indirizzi IP necessari dipende alla configurazione di gateway VPN che si vuole creare. Alcune configurazioni richiedono più indirizzi IP di altre. È consigliabile creare una subnet del gateway che usi /27 o /28.

Se viene visualizzato un errore che specifica che lo spazio indirizzi si sovrappone a una subnet o che la subnet non è inclusa nello spazio indirizzi della rete virtuale, controllare l'intervallo di indirizzi della rete virtuale. Gli indirizzi IP disponibili nell'intervallo di indirizzi creato per la rete virtuale potrebbero non essere sufficienti. Se la subnet predefinita copre l'intero intervallo di indirizzi, ad esempio, non rimarranno indirizzi IP per creare subnet aggiuntive. È possibile modificare le subnet nello spazio indirizzi esistente per liberare indirizzi IP oppure specificare un intervallo di indirizzi aggiuntivo e creare la subnet del gateway in tale intervallo.