# Implementación de Data Masking y Ofuscación en Bases de Datos Relacionales

**Entorno Técnico:**
- SO: Ubuntu Server 24.04 LTS 
- SGBD: MariaDB Server
- Lenguaje: SQL

**Objetivo Empresarial:**
Proteger la Información de Identificación Personal (PII) durante el ciclo de vida del desarrollo de software, implementando algoritmos de ofuscación irreversibles para habilitar entornos de QA y pruebas seguros sin alterar la integridad estructural de los datos de producción.

**Implementación y Comandos:**

1. **Aprovisionamiento y fortificación del motor de base de datos**

   ```bash
   sudo apt install mariadb-server
   sudo mysql_secure_installation

Verificación: El script interactivo de seguridad requerirá establecer la contraseña de root y purgará los accesos anónimos por defecto.

2. **Despliegue del esquema y segmentación de privilegios**

   ```sql
   sudo mysql -u root -p
   
   CREATE DATABASE pruebas;
   CREATE USER 'miusuario'@'localhost' IDENTIFIED BY 'miclave';
   GRANT ALL PRIVILEGES ON pruebas.* TO 'miusuario'@'localhost';
   FLUSH PRIVILEGES;
   USE pruebas;
   ```
Verificación: El motor devolverá Query OK tras cada ejecución, confirmando la creación del entorno aislado y la asignación correcta de permisos.

3. **Carga del modelo de datos e iteración del esquema**
 
   ```sql
   CREATE TABLE pruebas.mitabla (id MEDIUMINT NOT NULL AUTO_INCREMENT, nombre CHAR(30) NOT NULL, edad INTEGER(30), dni INTEGER(30), PRIMARY KEY (id));

   INSERT INTO mitabla (nombre, edad, dni) VALUES ("Jose", 34, 85888946), ("Maria", 54, 94638776), ("Francisco", 44, 98765439), ("Carmen", 78, 30099345), ("Mayte", 12,    23982387), ("Teresa", 10, 98730095), ("Cristina", 88, 38712095), ("Susana", 22, 77788823), ("Alfonso", 24, 12398756);

   ALTER TABLE mitabla ADD COLUMN estado VARCHAR(20);
   ALTER TABLE mitabla ADD COLUMN ciudad VARCHAR(20);

   UPDATE mitabla SET ciudad="Madrid"
   UPDATE mitabla SET estado="soltero";
   UPDATE mitabla SET estado="casado" WHERE nombre="Jose";
   UPDATE mitabla SET estado="casado" WHERE nombre="Maria";
   UPDATE mitabla SET estado="casado" WHERE nombre="Francisco";
   UPDATE mitabla SET estado="casado" WHERE nombre="Teresa";
   ```
Verificación: El comando UPDATE confirmará las filas modificadas (Rows matched), estableciendo el dataset base simulando datos de producción.

4. **Ejecución del algoritmo de anonimización (Enmascaramiento)**

   ```sql
   UPDATE mitabla SET nombre = concat('nom_', id, '_mask');
   UPDATE mitabla SET dni = id * 1000000;
   ```
Verificación: Ejecutar select * from mitabla;. La salida por consola mostrará los identificadores ofuscados con el patrón secuencial genérico y las identificaciones numéricas alteradas matemáticamente, confirmando la supresión de la información sensible.
