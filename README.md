# Errores al instalar módulos en Odoo 18 (Orfacorp)
Este documento registra los errores identificados durante la instalación de módulos en Odoo 18, con el fin de facilitar su análisis, corrección y seguimiento.

## Directorio app_store
#### **account_report_send_by_mail:** ❌
    - Estado: No instala
    - Error 1: Dependencia faltante
    - Error 2: Los módulos "MuK Backend Theme" y "Web Enterprise" no son incompatibles. (Despues de instalar account_reports)
    - Detalle: El módulo depende de account_reports, el cual no está disponible en el sistema

#### **advanced_web_domain_widget:** ✅
    - Estado: Instala, pero falla en ejecución
    - Área afectada: Interfaz web (pantalla en blanco)
    - Error: Falla en carga de assets JS
    - Detalle: Error al cargar @advanced_web_domain_widget/core/domain_selector domain_selector_operators (TypeError: _lt is not a function)
    - Impacto: La interfaz web no carga correctamente
    - Solución inmediata: Comentar los assets del módulo en el __manifest__.py

#### **aspl_stock_inventory_report_ee:** ✅
    - Estado: No instala
    - Archivo: views/res_config_setting.xml
    - Error: ParseError (herencia de vista)
    - Detalle: XPath apunta a <div id="production_lot_info"> inexistente en la vista padre res.config.settings
    - Solucion: En odoo 18 el elemento que contiene el id="production_lot_info" fue reemplazado ahora es <block>

#### **bi_warehouse_product_availability_in_so | eg_warehouse_restriction | invoice_stock_move | so_lot_multi_quantity_app | sync_inventory_adjustment** ✅
    - Estado: No instala
    - Archivo: addons/stock/data/stock_data.xml
    - Error: UniqueViolation (restricción de unicidad)
    - Detalle: Intento de crear nuevamente el almacén WH para la compañía 1; el registro ya existe (code, company_id duplicado)
    - Solucion:
        Verificar que el módulo stock esté correctamente instalado y actualizado
        En entorno de desarrollo, como última opción:
            - Eliminar manualmente los registros relacionados en stock_warehouse y stock_rule
            - Reiniciar Odoo y reinstalar el módulo stock

#### **dev_import_inventory** ✅
    - Estado: No instala
    - Error 1: Dependencia faltante
    - Error 2: Ya no se utilizan attrs y states
    - Detalle: El módulo depende de inventory_adjustment_cost, el cual no está disponible en el sistema
    - Solucion: instalar las dependencias y reemplazar los atributos attrs

#### **oi_base** ✅
    - Estado: No instala
    - Error: Dependencia externa faltante
    - Detalle: Falta la librería Python unidecode (no instalada en el entorno)
    - Solucion 1: pip install unidecode
    - Solucion 2: Se corrige el metodo patch para la version actual solo se permiten dos parametros y tambien se corrige rutas de importacion de RPCError, _t, CopyButton

#### **cqt_disable_quick_create** ✅
    - Estado: Instala, pero falla en ejecución
    - Área afectada: Interfaz web (pantalla en blanco)
    - Error: metodo path solo permite pasar dos argumentos como parametros
    - Solucion: Se corrige el metodo patch para la version actual solo se permiten dos parametros y se reemplaza web.rpc ya no se utiliza require ahora es "@web/core/network/rpc". Posterior a eso se refactoriza el path para utilizar orm.searchRead y comentamos el metodo boot

#### **portal_dashboard** ✅
    - Estado: Instala, pero falla en ejecución
    - Área afectada: Errores en js
    - Error: jsonrpc y referencia incorrecta de importacion en el js 
    - Solucion: En la version actual no existe jsonrpc en js ahora se utiliza rpc
---

## Directorio ec_location
#### **ec_tools** ✅
    - Estado: No instala
    - Error: Archivo manifest no valido
    - Detalle: Los modulos se encuentran en la version 16
    - Solucion: Se realiza mantenimiento para que funcione en la version 18

#### **ec_cash | ec_withholding | ec_account_base | ec_account_edi | ec_account_edi_load | ec_ats | ec_chart_template_update | ec_install | ec_payment_check | ec_payment_tc** ❌
    - Estado: No instala
    - Error 1: Archivo manifest no valido (ec_cash, ec_withholding)
    - Error 2: El modelo account.tax.template no existe
    - Detalle: Los modulos se encuentran en la version 16 (solucionado) el otro detalle es que en versiones anteriores existia el modelo account.tax.template, parece que en la version 18 fue removido o reemplazado

#### **ec_payment_check | ec_account_payment** 
    - Estado: No instala
    - Error: No existe is_internal_transfer en account_payment
    - Detalle: En la version 16 si existia, en la version 18 se utiliza destination_journal_id que indica una transferencia interna
    - Solucion: Omitir ese campo

#### **ec_chart_template_update**
    - Estado: No instala
    - Error: External Id no encontrado
    - Detalle: No existe la vista account_usability.view_account_account_template_tree

#### **ec_remision | ec_sri_authorizathions**
    - Estado: No instala
    - Error: Invalid field
    - Detalle: No encuentra el campo secure_sequence_id en account.journal en odoo 18 fue removido

---

## Directorio extras
#### **init_product** ✅
    - Estado: No instala
    - Error: No encuentra dependencia bom_ids
    - Detalle: Falta dependencia del modulo mrp en el manifest
    - Solucion: Anadir la dependencia faltante donde se encuentra el campo bom_ids modulo mrp

#### **app_odoo_users** ✅
    - Estado: No instala
    - Error: No encuentra ubicacion de las credenciales firebase
    - Detalle: El archivo de las credenciales se encuentra en la carpeta firebase recomendable colocar en variables de entorno, ademas habia una importacion recursiva en el archivo firebase_connection.py
    - Solucion: Se anade la ruta absoluta del archivo de credenciales (Cambiar esto ya en produccion) y se comenta la importacion mal definida del archivo .py antes mencionado

#### **cartera_odoo** ❌
    - Estado: No instala
    - Error: El campo check_count que esta en la vista res_partner no existe
    - Detalle: El campo check_count esta comentado en una herencia de res_partner revisar check.customer.line

#### **catalog_app** ✅
    - Estado: No instala
    - Error: Referencia incorrecta de la ruta de importacion
    - Detalle: Se estaba referenciando mal el archivo firebase_connection para obtener las credenciales firebase
    - Solucion: Se corrige la ruta de importacion

#### **direct_price_list** ✅
    - Estado: No instala
    - Error: No existe el modelo product.excel.extend
    - Detalle: Falta de dependencia en el manifest para el modelo product.excel.extend
    - Solucion 1: Se anade la dependencia imformes_ventas
    - Solucion 2: Ya no se acepta el valor false en la propiedad editable de los xml solo top o bottom
    - Observacion: Existe el mismo modelo product.excel.extend en el modulo informes_cartera

#### **importaciones_trueque** ❌
    - Estado: No instala
    - Error: Dependencias faltantes
    - Detalle: Está intentando instalar el módulo “ec_sri_authorizathions” que depende del módulo “account_accountant”.

#### **l10n_ec_reports** ❌
    - Estado: No instala
    - Error 1: Dependencias faltantes
    - Error 2: Los módulos "MuK Backend Theme" y "Web Enterprise" no son incompatibles.
    - Detalle: Está intentando instalar el módulo “l10n_ec_reports” que depende del módulo “account_reports”. luego de la instalacion de la dependencia salio el segundo error

#### **mejoras_ec_account_edi | validaciones_campos** ❌
    - Estado: No instala
    - Error 1: El modelo account.tax.template no existe
    - Detalle: En versiones anteriores existia el modelo account.tax.template, parece que en la version 18 fue removido o reemplazado

#### **mejoras_trueque** ✅
    - Estado: No instala
    - Error 1: No existe el campo is_internal_transfer en account.payment
    - Detalle: En versiones anteriores existia el campo is_internal_transfer, parece que en la version 18 fue removido o reemplazado

#### **odoo_import_rrhh_rules** ✅
    - Estado: No instala
    - Error: Importaciones comentadas y dependencias faltantes
    - Detalle: Falta dependencia hr_work_entry_contract_enterprise y descomentar los archivos init y views
    - Solucion: anadir la dependencia faltante en manifest y descomentar las referencias de los archivos python y xml

#### **woocomerce_odoo_trueque** ✅
    - Estado: No instala
    - Error: Error propiedad t-field en vista kanban y propiedad numbercall en vistas cron ya no existen
    - Detalle: En la version 18 se omiten esas propiedades
    - Solucion: Quitar esas propiedades
---

## Directorio oca
#### **account_template_active** ❌
    - Estado: No instala
    - Error: No encuentra la referencia de una vista xml
    - Detalle: Este modulo depende de account_usability y no encuentra la vista xml a la que hereda
    - Observacion: Omitir este modulo


## Directorio orfa
#### **custom_hr_contract | import_mo_with_series | informes_catalogo | orfa_aprobaciones | orfa_caja_chica | orfa_presupuestos_diarios_modulev2 | orfa_seguimiento_de_pedidos | orfa_ubicacion_lotes** ✅
    - Estado: No instala
    - Error: Vista xml no valida error de atributos attrs
    - Detalle: Ya no se utiliza los atributos attrs en versiones actuales
    - Solucion: Reemplazar esos valores por la forma actual que se aplica. Ej. antiguo: attrs="{ 'invisible': [(condicion)] }". actual invisible="condicion"

#### **custom_sale_product_number_pairs | inventory_adjustment_cost** ✅
    - Estado: No instala
    - Error: Xpath no detecta elemento localizado con tree
    - Detalle: En version actual el tree es reemplazado por list
    - Solucion: Cambiar la expresion en vez de tree colocar list

#### **orfa_audit_excel_import | orfa_bd_vendedores | orfa_campos_personalizados | orfa_capacidad_bodega | orfa_presupuestos_diarios_modulev2 | orfa_reporte_fotos | orfa_res_zones | orfa_return_module | orfa_seguimiento_de_pedido | orfa_ubicacion_lotes | post_request** ✅
    - Estado: No instala
    - Error: Vista Xml mal estructurada
    - Detalle: Se define vista tree pero en versiones actuales se usa list
    - Solucion: Reemplazar la vista tree por list

#### **orfa_pricelist_user_access** ✅
    - Estado: No instala
    - Error: Xpath expresion no encuentra elemento
    - Detalle: pricelist_config ya no se encuentra en la vista nativa en lugar usar pricelist_settings
    - Solucion: Reemplazar pricelist_config por pricelist_settings

#### **orfa_stock_accounting_fix** ✅
    - Estado: No instala
    - Error: Campo no existe en modelo mr.production
    - Detalle: El campo date_planned_start no existe en mr.production se reviso en el modelo nativo y demas directorios y no existe ese campo
    - Solucion: Se anade campo date_planned_start y se ajusta el menuitem para que se muestre el wizar en el menu de contabilidad

#### **orfa_negar_cupo** ❌
    - Estado: No instala
    - Error: No encuentra la expresion xpath approve_cupoo y global_disc
    - Detalle: falta dependencia en el manifest esos campos se los define en el modulo cartera_odoo

#### **rest_api_warehouse_filter** ✅
    - Estado: No instala
    - Error: Error de referencia import en archivo controller
    - Detalle: Referencia mal colocada de rest_api_odoo
    - Solucion: Revisar bien la ruta y colocar la referencia correctamente para obtener la clas RestApi y DateTimeEncoder

#### **showkase_integration** ✅
    - Estado: No instala
    - Error: Error numbercall cron 
    - Detalle: En version actual ya no se aplica numbercall en los cron
    - Solucion: comentar numbercall

#### **facturas_de_reembolso** ❌
    - Estado: No instala
    - Error: El campo secure_sequence_id no existe en account.journal
    - Detalle: En versiones anteriores el campo existia ahora fue removido o reemplazado para la version 18

#### **generate_ean13** ❌
    - Estado: No instala
    - Error: Expresion xpath no encontrada sale_ebay
    - Detalle: No se encuentra el campo sale_ebay en ningun otro modulo

#### **informes_cartera | orfa_anadir_analiticas | orfa_aprobacion_pagos | orfa_liquidacion_compras | orfa_viaticos** ❌
    - Estado: No instala
    - Error 1: El modelo account.tax.template no existe
    - Detalle: En versiones anteriores existia el modelo account.tax.template, parece que en la version 18 fue removido o reemplazado

#### **informes_pagos | sincronizar_clientes** ✅
    - Estado: No instala
    - Error 1: No existe el campo is_internal_transfer en account.payment
    - Detalle: En versiones anteriores existia el campo is_internal_transfer, parece que en la version 18 fue removido o reemplazado

#### **orfa_negar_cupo** ❌
    - Estado: No instala
    - Error: El campo check_count que esta en la vista res_partner no existe, depende de cartera_odoo
    - Detalle: El campo check_count esta comentado en una herencia de res_partner revisar check.customer.line

#### **presale_order** ❌
    - Estado: No instala
    - Error: El campo blocked no existe en res_partner
    - Detalle: El campo blocked no existe en res_partner y partner_blocked lo esta utilizando en un related

