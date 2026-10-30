## 1. Sello digital y firma de PDF

### Qué es y cómo funciona

* **Diferencia legal en Argentina (Ley 25.506 y Dec. 182/2019)**:
* **Firma Electrónica**: Cualquier método de identificación o consentimiento digital que no cumpla con los requisitos formales de la ley (ej. login con JWT, PIN, hash simple en base de datos, aceptación de términos). Es válida legalmente, pero en caso de litigio la **carga de la prueba** recae sobre quien sostiene su validez.
* **Firma Digital**: Utiliza certificados digitales X.509 emitidos por un Certificador Licenciado bajo la cadena oficial de la Infraestructura de Clave Pública de la República Argentina (PKI ONTI / Secretaría de Innovación Pública). Otorga **presunción de autoría e integridad** (equivalente legal a la firma ológrafa certificada ante escribano) e **invierte la carga de la prueba** (quien desconozca la firma debe demostrar que fue adulterada o vulnerada).


* **Mecanismos técnicos aplicables**:
1. **Firma criptográfica PAdES (ETSI EN 319 142)**: Incorpora una firma PKCS#7 / CMS directamente en el incremental update del archivo PDF. Permite incluir un sellado de tiempo (RFC 3161 - Timestamping Authority / TSA) y validación de revocación (OCSP / CRL) para preservación a largo plazo (PAdES-LTA).
2. **Sello con Código QR y Portal de Validación**: El backend computa el hash criptográfico (SHA-256) del PDF final antes de firmar o al persistir el reporte. Se genera un QR que enlaza a una URL pública de consulta rápida (`/validar-informe?id=UUID&hash=SHA256`) para auditorías directas sin software lector de PDF.



---

### Riesgos y Beneficios

#### Beneficios

* **Inalterabilidad e Integridad Estricta**: La firma PAdES invalida visual y programáticamente el documento si se modifica un solo bit del contenido o metadatos una vez sellado.
* **Auditoría Externa Sin Intermediarios**: Cualquier visor estándar (Adobe Acrobat Reader, navegadores o herramientas CLI) valida la cadena de certificados y el resumen SHA sin consultar necesariamente a tu API.
* **Validez Jurídica Robusta**: Para informes técnicos o balances (como emisiones de GEI / huella de carbono), la firma digital de un profesional o de la entidad certificadora le da fuerza probatoria ante inspecciones, bancos o auditorías ITEC/ISO.
* **Desacoplamiento con QR**: Permite verificar documentos impresos en papel contrastando los datos mostrados en pantalla tras el escaneo con los datos físicos del informe.

#### Riesgos y Desafíos

* **Gestión y Custodia de Claves Privadas**:
* Si la firma la realiza el servidor de manera desatendida (sello de persona jurídica), la clave privada (.p12/.pfx) debe asegurarse en un KMS (Key Management Service) o HSM; su filtración compromete la identidad legal de la entidad emisora.
* Si la firma es de un profesional matriculado, la PKI argentina exige comúnmente tokens criptográficos físicos (FIPS 140-2 Nivel 2/3) o firma remota autorizada, lo que dificulta la firma 100% automatizada sin intervención del usuario.


* **Complejidad en Concurrencia y Performance**: El cálculo criptográfico sobre archivos binarios pesados (generación de digest y firma CMS) consume CPU. En esquemas de generación masiva de reportes se requiere procesamiento asíncrono o colas de trabajo.
* **Expiración y Revocación de Certificados**: Sin un servicio TSA (Time-Stamping Authority) configurado, al vencer el certificado del emisor el visor de PDF puede marcar la firma con advertencias o como no verificable.
* **Bloqueo de Modificaciones Posteriores**: Operaciones post-procesamiento sobre el PDF (comprimir con Ghostscript, añadir marcas de agua o concatenar páginas) destruyen la firma criptográfica existente.

---

### Implementaciones y Stack Tecnológico

#### Arquitectura de Procesamiento en Backend (Java / Spring Boot)

1. **Generación del Documento**: Ensamblado del layout, tablas y gráficos de emisiones en memoria o stream temporal (usando OpenPDF o PDFBox).
2. **Cálculo de Hash y QR**: Inserción del pie de página con el código QR apuntando al endpoint de verificación y al hash previsto.
3. **Cierre y Firma Criptográfica**: Pasaje del stream del PDF por el motor de firma utilizando `Bouncy Castle` para resolver el envelope CMS/PKCS#7 contra el Keystore (`PKCS12` o proveedor `SunPKCS11`).
4. **Persistencia y Congelamiento**: Almacenamiento del PDF inmutable en almacenamiento de objetos (S3 / volumen local) y registro del SHA-256 en la base de datos junto con el estado del trámite.

#### Librerías Principales

* **Apache PDFBox 3.x (`org.apache.pdfbox`)**:
* `org.apache.pdfbox.pdmodel.interactive.digitalsignature.SignatureInterface`: Interfaz central para implementar la firma asimétrica del payload.
* Soporta adición de firmas visuales (rectángulo con datos del firmante) y firmas invisibles.


* **Bouncy Castle (`bcprov-jdk18on` / `bcpkix-jdk18on`)**:
* Motor de bajo nivel para serializar las estructuras ASN.1/CMS, resolver certificados intermedios y conectar con clientes TSA / OCSP.


* **ZXing (`com.google.zxing:javase`)**: Generación del mapa de bits del código QR para inyección directa como imagen en el PDF previo al firmado.

---

### Documentación y Referencias Técnicas

* **Marco Legal y Normativo (Argentina)**:
* *Ley N° 25.506 de Firma Digital* y su *Decreto Reglamentario N° 182/2019*: Definición de firma digital, electrónica, certificadores licenciados y presunciones legales.
* *Estándares de la ONTI (Oficina Nacional de Tecnologías de Información)*: Guías técnicas sobre la cadena de confianza PKI raíz y perfiles de certificados de la República Argentina.


* **Estándares Criptográficos y de Formato**:
* *ETSI EN 319 142*: Estándar técnico de perfiles PAdES (PAdES-B-B, PAdES-B-T con timestamp).
* *RFC 5652 (Cryptographic Message Syntax - CMS)* y *RFC 3161 (Internet X.509 PKI Time-Stamp Protocol)*.


* **Guías y Código Oficial**:
* *Apache PDFBox Examples*: Implementación de referencia oficial de `CreateSignature.java` y `CreateVisibleSignature.java` disponible en el repositorio de PDFBox.
