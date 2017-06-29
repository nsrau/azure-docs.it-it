
Ogni endpoint ha una *porta pubblica* e una *porta privata*:

* La porta pubblica viene usata dal servizio di bilanciamento del carico di Azure per restare in attesa di traffico in ingresso sulla macchina virtuale da Internet.
* La porta privata viene usata dalla macchina virtuale per restare in attesa di traffico in ingresso, in genere destinato a un'applicazione o a un servizio in esecuzione nella macchina virtuale.

I valori predefiniti per il protocollo IP e le porte TCP o UDP per i protocolli di rete noti vengono forniti quando si creano endpoint con il portale di Azure. Per gli endpoint personalizzati, è necessario specificare il protocollo IP corretto (TCP o UDP) e le porte pubbliche e private. Per distribuire il traffico in ingresso in modo casuale tra più macchine virtuali, è necessario creare un set con carico bilanciato costituito da più endpoint.

Dopo aver creato un endpoint, è possibile usare un elenco di controllo di accesso (ACL) per definire regole che autorizzano o rifiutano il traffico in ingresso alla porta pubblica dell'endpoint in base al relativo indirizzo IP di origine. Tuttavia, se la macchina virtuale è in una rete virtuale di Azure, è consigliabile usare invece i gruppi di sicurezza di rete. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](../articles/virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> La configurazione del firewall per le macchine virtuali di Azure viene eseguita automaticamente per le porte associate agli endpoint di connettività remota configurati automaticamente da Azure. Per le porte specificate per tutti gli altri endpoint, non viene effettuata alcuna configurazione automatica del firewall della macchina virtuale. Quando si crea un endpoint per la macchina virtuale, è necessario assicurarsi che il firewall della macchina virtuale consenta anche il traffico per il protocollo e la porta privata corrispondente alla configurazione dell'endpoint. Per configurare il firewall, vedere la documentazione o la Guida in linea per il sistema operativo in esecuzione sulla macchina virtuale.
>
>

## <a name="create-an-endpoint"></a>Creare un endpoint
1. Accedere al [portale di Azure](https://portal.azure.com), se questa operazione non è già stata eseguita.
2. Fare clic su **Macchine virtuali**e quindi scegliere il nome della macchina virtuale da configurare.
3. Nella gruppo **Impostazioni** fare clic su **Endpoint**. Nella pagina **Endpoint** sono elencati tutti gli endpoint correnti per la macchina virtuale. Questo esempio è relativo a una VM Windows. Una macchina virtuale Linux mostrerà per impostazione predefinita un endpoint per SSH.

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Endpoint](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. Nella barra dei comandi sopra le voci di endpoint fare clic su **Aggiungi**.
5. Nella pagina **Aggiungere un endpoint** digitare un nome per l'endpoint in **Nome**.
6. In **Protocollo** scegliere **TCP** o **UDP**.
7. In **Porta pubblica** digitare il numero di porta per il traffico in ingresso da Internet. In **Porta privata**, digitare il numero di porta su cui la macchina virtuale è in attesa. Questi numeri di porta possono essere diversi. Assicurarsi che il firewall nella macchina virtuale sia stato configurato per consentire il traffico corrispondente al protocollo (nel passaggio 6) e la porta privata.
10. Fare clic su **OK**.

Il nuovo endpoint verrà elencato nella pagina **Endpoint** .

![Creazione dell'endpoint completata](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Gestire l'elenco di controllo di accesso su un endpoint
Per definire il set di computer che può inviare il traffico, l'elenco di controllo di accesso in un endpoint può limitare il traffico in base all'indirizzo IP di origine. Per aggiungere, modificare o rimuovere un elenco di controllo di accesso su un endpoint, attenersi alla procedura seguente.

> [!NOTE]
> se l'endpoint fa parte di un set con carico bilanciato, qualsiasi modifica apportata all'elenco di controllo di accesso su un endpoint verrà applicata a tutti gli endpoint del set.
>
>

Se la macchina virtuale si trova in una rete virtuale di Azure, è consigliabile usare i gruppi di sicurezza di rete anziché gli elenchi di controllo di accesso. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](../articles/virtual-network/virtual-networks-nsg.md).

1. Accedere al portale di Azure, se questa operazione non è già stata eseguita.
2. Fare clic su **Macchine virtuali**e quindi scegliere il nome della macchina virtuale da configurare.
3. Fare clic su **Endpoint**. Selezionare l'endpoint appropriato nell'elenco. L'elenco di controllo si trova nella parte inferiore della pagina.

   ![specificare i dettagli di ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Usare le righe nell'elenco per aggiungere, eliminare o modificare le regole per un elenco di controllo di accesso e modificarne l'ordine. Il valore **Subnet remota** è un intervallo di indirizzi IP per il traffico in ingresso da Internet usato dal servizio di bilanciamento del carico di Azure per autorizzare o rifiutare il traffico in base all'indirizzo IP di origine. Assicurarsi di specificare l'intervallo di indirizzi IP nel formato CIDR, noto anche come formato di prefisso di indirizzo. Un esempio è `10.1.0.0/8`.

 ![Nuova voce ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


È possibile usare regole per consentire solo il traffico da computer specifici corrispondenti ai computer su Internet oppure rifiutare il traffico da intervalli di indirizzi specifici e noti.

Le regole sono valutate nell'ordine, dalla prima fino all'ultima. Questo significa che le regole devono essere ordinate dalla meno restrittiva alla più restrittiva. Per alcuni esempi e altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](../articles/virtual-network/virtual-networks-acl.md).
