# iTop-Combodo-origin-Whatsapp

**Repositorio:** iTop-Combodo-origin-Whatsapp  
**Autor:** Juan Guilarte  
**Fecha:** 2025-07-23

---

## iTop: Agregar "whatsapp" como opción en el campo `origin` de los tickets (UserRequest)

Esta guía documenta el procedimiento **completo y probado** para agregar `"whatsapp"` como valor permitido en el campo `origin` de los tickets (UserRequest) en **iTop 3.x** bajo **CentOS 7**. Incluye tanto la actualización del modelo, como los pasos de permisos, regeneración de caché y validaciones por la web y API.

---

## 1. Editar los archivos del modelo de datos

Ubica y edita estos **3 archivos XML** (adaptar ruta según versión o customizaciones locales):

- `/var/www/html/web/datamodels/2.x/itop-request-mgmt/datamodel.itop-request-mgmt.xml`
- `/var/www/html/web/datamodels/2.x/itop-request-mgmt-itil/datamodel.itop-request-mgmt-itil.xml`
- `/var/www/html/web/datamodels/2.x/itop-incident-mgmt-itil/datamodel.itop-incident-mgmt-itil.xml`

Agrega el siguiente bloque dentro de `<values>` de `<field id="origin" ...>`:

```xml
<value id="whatsapp">
    <code>whatsapp</code>
</value>
```

Debe quedar así:

```xml
                <field id="origin" xsi:type="AttributeEnum">
                    <values>
                        <value id="mail">
                            <code>mail</code>
                        </value>
                        <value id="phone">
                            <code>phone</code>
                        </value>
                        <value id="portal">
                            <code>portal</code>
                        </value>
                        <value id="monitoring">
                            <code>monitoring</code>
                        </value>
                        <value id="whatsapp">
                            <code>whatsapp</code>
                        </value>
                    </values>
                    <sql>origin</sql>
                    <default_value>phone</default_value>
                    <is_null_allowed>true</is_null_allowed>
                </field>
```

---

## 2. Dar permisos a las carpetas de caché

Otorga permisos completos sobre la caché para evitar problemas durante la recompilación del modelo:

```bash
chown -R apache:apache /var/www/html/web/data/cache-production
chmod -R 777 /var/www/html/web/data/cache-production
chown -R apache:apache /var/www/html/web/data/cache-production-build
chmod -R 777 /var/www/html/web/data/cache-production-build
```

---

## 3. Borrar la caché anterior

```bash
rm -rf /var/www/html/web/data/cache-production/*
rm -rf /var/www/html/web/data/cache-production-build/*
```

---

## 4. Haz editable el archivo de configuración

```bash
chmod 664 /var/www/html/web/conf/production/config-itop.php
```

---

## 5. Ejecuta el Setup Wizard

Abre en el navegador:

```
http://TU_SERVIDOR/web/setup/
```

En QA fue:
```
http://10.130.7.5/web/setup/
```


- Haz clic en **Siguiente** hasta finalizar el proceso **(NO MODIFICAR NADA)**.
- Ignora warnings de permisos si solo son advertencias y el proceso termina correctamente.
- Si el wizard se detiene al 20% (Compiling the data model), verifica y corrige los permisos de las carpetas de caché y vuelve a intentarlo.

---

## 6. Restaura permisos al archivo de configuración

```bash
chmod 644 /var/www/html/web/conf/production/config-itop.php
```

---

## 7. Valida que el modelo fue recompilado

Verifica que “whatsapp” aparece en el modelo de datos real:

```bash
grep -i whatsapp /var/www/html/web/data/datamodel-production.xml
```

Si aparece, el valor ya está disponible para web y API.

---

## 8. Prueba en la web y la API

- En el portal de iTop, ahora puedes seleccionar “Whatsapp” en el campo origen de un ticket.
- En la API, puedes usar `"origin": "whatsapp"` en la creación de tickets.

---

## Resultado

✅ **¡Funciona!**
- `"whatsapp"` ahora es un valor permitido y usable en el campo `origin`.
- El cambio es efectivo tanto en la interfaz web como por la API.

---

## Tips y recomendaciones

- Recuerda restaurar permisos más seguros (`775` o menos) si el entorno es productivo.
- Para futuros cambios en el modelo, repite los pasos de permisos y setup wizard.
- Haz backups del modelo real (`datamodel-production.xml`) antes de modificaciones.

---

**Repositorio:** iTop-Combodo-origin-Whatsapp  
**Autor:** Juan Guilarte  
**Fecha:** 2025-07-23

