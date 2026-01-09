# Errores al instalar módulos en Odoo 18 (Orfacorp)
Este documento registra los errores identificados durante la instalación de módulos en Odoo 18, con el fin de facilitar su análisis, corrección y seguimiento.

## Directorio app_store
#### **account_report_send_by_mail:**
    - Estado: No instala
    - Error: Dependencia faltante
    - Detalle: El módulo depende de account_reports, el cual no está disponible en el sistema

#### **advanced_web_domain_widget:**
    - Estado: Instala, pero falla en ejecución
    - Área afectada: Interfaz web (pantalla en blanco)
    - Error: Falla en carga de assets JS
    - Detalle: Error al cargar @advanced_web_domain_widget/core/domain_selector domain_selector_operators (TypeError: _lt is not a function)
    - Impacto: La interfaz web no carga correctamente
    - Solución inmediata: Comentar los assets del módulo en el __manifest__.py

#### **aspl_stock_inventory_report_ee:** 
    - Estado: No instala
    - Archivo: views/res_config_setting.xml
    - Error: ParseError (herencia de vista)
    - Detalle: XPath apunta a <div id="production_lot_info"> inexistente en la vista padre res.config.settings

#### **bi_warehouse_product_availability_in_so | eg_warehouse_restriction | invoice_stock_move | so_lot_multi_quantity_app | sync_inventory_adjustment**
    - Estado: No instala
    - Archivo: addons/stock/data/stock_data.xml
    - Error: UniqueViolation (restricción de unicidad)
    - Detalle: Intento de crear nuevamente el almacén WH para la compañía 1; el registro ya existe (code, company_id duplicado)
    - Solucion:
        Verificar que el módulo stock esté correctamente instalado y actualizado
        En entorno de desarrollo, como última opción:
            - Eliminar manualmente los registros relacionados en stock_warehouse y stock_rule
            - Reiniciar Odoo y reinstalar el módulo stock

#### **dev_import_inventory**
    - Estado: No instala
    - Error: Dependencia faltante
    - Detalle: El módulo depende de inventory_adjustment_cost, el cual no está disponible en el sistema

#### **oi_base**
    - Estado: No instala
    - Error: Dependencia externa faltante
    - Detalle: Falta la librería Python unidecode (no instalada en el entorno)
    - Solucion: pip install unidecode

