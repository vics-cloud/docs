**Další možnosti pro selfhosting bez `sudo` (použitím Node.js/PHP)** 

---

### **1. PHP File Manager (např. Kodi, FileRun)**
- **Kodi**: Jednoduchý PHP filebrowser s podporou uploadu, editace a správy souborů. Stačí nahrát soubory na server a přistupovat přes webové rozhraní.  
  *Příklad*:  
  ```php
  <?php
  if ($_FILES["file"]["error"] == UPLOAD_ERR_OK) {
      move_uploaded_file($_FILES["file"]["tmp_name"], "files/" . $_FILES["file"]["name"]);
  }
  ?>
  ```
  **Výhody**: Žádná instalace, funguje i na free hostingu (např. InfinityFree) .

- **FileRun**: Pokročilý správce souborů s podporou API, vyhledávání a cloudovou integrací. Vyžaduje PHP 7+ a MySQL, ale nepotřebuje `sudo` .

---

### **2. Node.js + Frameworky (např. H5AI, Dropzone.js)**
- **H5AI**: Moderní webový filebrowser s prémiovým vzhledem. Lze integrovat do Express.js:  
  ```javascript
  const express = require('express');
  const app = express();
  app.use(express.static('public'));
  app.listen(3000);
  ```
  Nahrát H5AI do složky `public` a přístup přes `http://localhost:3000` .

- **Dropzone.js**: Knihovna pro drag-and-drop upload. Kombinujte s Express.js:  
  ```javascript
  app.post('/upload', (req, res) => {
      req.pipe(fs.createWriteStream(`uploads/${req.headers['x-file-name']}`));
      res.sendStatus(200);
  });
  ```

---

### **3. Webové IDE (např. CodeSandbox, CodeServer)**
- **CodeServer**: Selfhostovaná verze VS Code. Spusťte bez `sudo` na portu 8080:  
  ```bash
  npx code-server --auth none --port 8080
  ```
  **Výhody**: Integrovaný správce souborů, editace kódu a terminál .

---

### **4. Cloudové řešení (Glitch, Replit)**
- **Glitch.com**: Hostujte Node.js aplikace přímo v prohlížeči. Stačí nahrát `server.js` a soubory.  
  *Příklad*:  
  ```javascript
  const http = require('http');
  http.createServer((req, res) => { /* ... */ }).listen(3000);
  ```
  **Výhody**: Automatické nasazení, bez nutnosti SSH/sudo .

---

### **5. Statické generátory (např. Jekyll, Hugo)**
- **Hugo**: Generujte statické stránky s možností uploadu přes Git nebo FTP.  
  ```bash
  hugo new site my-site && cd my-site
  hugo server -D --port 3000
  ```
  **Výhody**: Bezpečné (žádné serverové skripty), rychlé nasazení .

---

### **6. Bezpečnostní doporučení** 
1. **Omezení přístupu**: Použijte `.htaccess` (PHP) nebo middleware pro autentizaci (Node.js).  
2. **HTTPS**: Využijte Let's Encrypt přes reverse proxy (např. Cloudflare).  
3. **Firewall**: Blokujte neznámé IP adresy v kódu:  
   ```javascript
   app.use((req, res, next) => {
       if (req.ip !== '192.168.1.1') res.status(403).send('Forbidden');
       else next();
   });
   ```

---

**Shrnutí**: Pokud nemáte `sudo`, zaměřte se na:  
- **PHP**: TinyFileManager, Kodi, FileRun.  
- **Node.js**: Express.js + H5AI/Dropzone, CodeServer.  
- **Cloud**: Glitch, Replit pro rychlé nasazení.  
- **Statické nástroje**: Hugo/Jekyll pro jednoduchost . 

Pro detaily navštivte [FileRun](https://filerun.com) nebo [CodeServer docs](https://coder.com/docs).
