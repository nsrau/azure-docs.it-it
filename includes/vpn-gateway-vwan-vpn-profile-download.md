---
title: includere il file
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066207"
---
## <a name="1-download-the-file"></a>1. scaricare il file

Eseguire i comandi seguenti. Copiare l'URL dei risultati nel browser per scaricare il file zip del profilo.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Estrarre il file zip

Estrarre il file con estensione zip. Il file contiene le cartelle seguenti:

* AzureVPN
* Generico
* OpenVPN (se sono state abilitate le impostazioni di autenticazione OpenVPN e Azure AD sul gateway. Per il gateway VPN, vedere [creare un tenant](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Per la rete WAN virtuale, vedere [creare un tenant-VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. recuperare informazioni

Nella cartella **AzureVPN** passare al file ***azurevpnconfig. XML*** e aprirlo con il blocco note. Prendere nota del testo tra i seguenti tag.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Dettagli profilo

Quando si aggiunge una connessione, usare le informazioni raccolte nel passaggio precedente per la pagina dei dettagli del profilo. I campi corrispondono alle informazioni seguenti:

   * **Destinatari:** Identifica la risorsa destinatario a cui è destinato il token
   * **Autorità di certificazione:** Identifica il servizio token di sicurezza (STS) che ha emesso il token e il tenant di Azure AD
   * **Tenant:** Contiene un identificatore univoco e non modificabile del tenant di directory che ha emesso il token.
   * **FQDN:** Nome di dominio completo (FQDN) nel gateway VPN di Azure
   * **ServerSecret:** Chiave precondivisa del gateway VPN

## <a name="folder-contents"></a>Contenuto della cartella

* La **cartella Generic** contiene il certificato del server pubblico e il file file vpnsettings. XML. Il file file vpnsettings. XML contiene le informazioni necessarie per configurare un client generico.

* Il file zip scaricato può contenere anche cartelle **WindowsAmd64** e **WindowsX86** . Queste cartelle contengono il programma di installazione per SSTP e IKEv2 per i client Windows. Per installarli, è necessario disporre dei diritti di amministratore per il client.