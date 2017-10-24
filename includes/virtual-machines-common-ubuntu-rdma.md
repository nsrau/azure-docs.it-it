1. Installare dapl, rdmacm, ibverbs e mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. In /etc/waagent.conf abilitare RDMA rimuovendo il commento dalle righe di configurazione seguenti. Per modificare questo file è necessario l'accesso alla radice.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Aggiungere o modificare le impostazioni della memoria seguenti espresse in KB nel file /etc/security/limits.conf. Per modificare questo file è necessario l'accesso alla radice. A scopo di test è possibile impostare memlock su unlimited. Ad esempio: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Installare la libreria Intel MPI. [Acquistare e scaricare](https://software.intel.com/intel-mpi-library/) la libreria da Intel o scaricare la [versione di valutazione gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
 wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/11595/l_mpi_2017.3.196.tgz
   ```
 
 Per la procedura di installazione, vedere [Intel MPI Library installation guide](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) (Guida all'installazione di Intel MPI Library).

5. Abilitare ptrace per i processi non di debugger non radice (necessari per le versioni più recenti di Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
