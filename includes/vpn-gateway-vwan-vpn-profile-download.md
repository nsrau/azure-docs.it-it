---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066207"
---
## <a name="1-download-the-file"></a>1. Scaricare il file

Eseguire i comandi seguenti. Copiare l'URL del risultato nel browser per scaricare il file zip del profilo.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Estrarre il file zip

Estrarre il file con estensione zip. Il file contiene le seguenti cartelle:

* AzureVPN
* Generico
* OpenVPN (se sono state abilitate le impostazioni di autenticazione OpenVPN e Azure AD nel gateway. Per il gateway VPN, vedere [Creare un tenant.](../articles/vpn-gateway/openvpn-azure-ad-tenant.md) Per la rete WAN virtuale, vedere [Creare un tenant - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Recuperare informazioni

Nella cartella **AzureVPN** passare al file ***azurevpnconfig.xml*** e aprirlo con Il Blocco note. Prendere nota del testo tra i tag seguenti.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Dettagli del profilo

Quando si aggiunge una connessione, utilizzare le informazioni raccolte nel passaggio precedente per la pagina dei dettagli del profilo. I campi corrispondono alle seguenti informazioni:

   * **Pubblico:** Identifica la risorsa del destinatario a cui è destinato il token
   * **Emittente:** Identifica il servizio token di sicurezza (STS) che ha generato il token e il tenant di Azure AD
   * **Inquilino:** Contiene un identificatore univoco non modificabile del tenant di directory che ha emesso il token
   * **NOME di dominio completo:** Il nome di dominio completo (FQDN) nel gateway VPN di AzureThe fully qualified domain name (FQDN) on the Azure VPN gateway
   * **ServerSecret:** Chiave precondivisa del gateway VPN

## <a name="folder-contents"></a>Contenuto della cartella

* La **cartella generica** contiene il certificato del server pubblico e il file VpnSettings.xml. Il file VpnSettings.xml contiene le informazioni necessarie per configurare un client generico.

* Il file zip scaricato può contenere anche cartelle **WindowsAmd64** e **WindowsX86.** Queste cartelle contengono il programma di installazione per SSTP e IKEv2 per i client Windows. Per installarli, è necessario disporre dei diritti di amministratore sul client.