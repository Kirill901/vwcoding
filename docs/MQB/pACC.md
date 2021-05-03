disqus: https-mqb-readthedocs-io
# Активация ACC и pACC (SWAP)

!!! warning ""
    Все действия вы делаете на свой страх и риск! Мы не несем ответственность за испорченное оборудование  
    Архив с генератором SWAP, прошивками и параметриями можно скачать [тут](../firmwares/accGenerator.zip)  

Для активации адаптивного круиз контроля необходимо сначала узнать текущую версию радара, который установлен:  
```
Обозначение системы: ACCCONTIMQB  
Версия ПО: 0372  
Версия АО: H01  
Номер детали VW/Audi: 2Q0907572R  
Номер детали АО: 2Q0907572B  
```

pACC (Predictive ACC) — это адаптивный круиз-контроль, который может автоматически устанавливать скорость движения с помощью картографических данных (прогнозируемых данных, PSD) и распознаваемых дорожных знаков.  

??? tip "Функциональные FEC коды"
    10009001	MRR-Paket 1: ACClow (Basis-ACC) + FrontAssist inkl. CityANB  
    10009002	MRR-Paket 2: ACClow (ACC FTS) + FrontAssist inkl. CityANB   
    10009003	MRR-Paket 3: ACClow (ACC S&G) + FrontAssist inkl. CityANB   
    10009004	MRR-Paket 4: FrontAssist inkl. CityANB (ohne ACC)   
    10009005	MRR-Paket 5: CityANB (ohne ACC)   
    10009006	MRR-Paket 6: ACChigh (Basis-ACC) + FrontAssist inkl. CityANB   
    10009007	MRR-Paket 7: ACChigh (ACC FTS) + FrontAssist inkl. CityANB    
    10009008	MRR-Paket 8: ACChigh (ACC S&G) + FrontAssist inkl. CityANB   
    10009101	ACC-Funktionserweiterungs-Paket "predictiveACC"   
    10009102	ACC-Funktionserweiterungs-Paket "StauAssistent"   
    10009103	ACC-Funktionserweiterungs-Paket "predictiveACC&StauAssistent"   
    10009201	AWV-Auspraegung "AWV1,2 - Warnung nur visuell&auditiv"   
    10009202	AWV-Auspraegung "AWV1,2"   
    10009203	AWV-Auspraegung "AWV1,2,3"   
    10009204	AWV-Auspraegung "AWV1,2,3, vFGS   
    10009205	AWV-Auspraegung „AWV1,2,3, vFGS, vRFS“  
    10009301	AWV-Funktionserweiterungs-Paket "Elektronische Parkbremse"  
    10009301	AWV-Funktionserweiterungs-Paket "EmergencyAssist"   
    10009302	AWV-Funktionserweiterungs-Paket "Abbiegeassistent"   
    10009303	AWV-Funktionserweiterungs-Paket "AWV-Gegenverkehr"   
    10009304	AWV-Funktionserweiterungs-Paket "Abbiegeassistent&AWV-Gegenverkehr"   
    10009305	AWV-Funktionserweiterungs-Paket "EmergencyAssist&AWV-Gegenverkehr"   
    10009306	AWV-Funktionserweiterungs-Paket "EmergencyAssist&Abbiegeassistent"   
    10009307	AWV-Funktionserweiterungs-Paket "EmergencyAssist&Abbiegeassistent&AWV-Gegenverkehr"   
    10009500	Verkehrszeichenerkennung (VZE)  
    10009600	Vorrausschauender Fussgaengerschutz (VFS) - FCWP  


Для генерации SWAP кода необходимо проделать следующие шаги:

1. Принудительно на радаре активировать защиту компонентов через ODIS Online снять, либо у местного дилера VW, либо у каких-нибудь контор, которые занимаются активацией, свапами и т.д.  

2. Установка X прошивки. Например, для радара, который указан выше - FL_2Q0907572R_X371___S.odx  

3. Снять защиту компонентов через ODIS Online снять, либо у местного дилера VW, либо у каких-нибудь контор, которые занимаются активацией, свапами и т.д.  

4. Для генерирования SWaP кодов понадобится VCRN (Vehicle Component Registration Number).  
Данный код можно вытянуть из измеряемых величин 13 блока через ODIS E (measurement function) или OBD11 (control unit -> Live Data -> Individualization characteristic)  
   
5. Выбор нужных FEC кодов  
```
10009008 - ACC High 210 & stop and go & fts  
10009204 - front assist  
10009101 - pre acc  
```

6. С помощью утилиты afcg.exe сгенерировать SWAP код. Для генерации кода понадобится ввести: VIN, VCRN (из 3 шага) и набор FEC кодов через пробел     

7. Полученный SWAP код необходимо ввести в адаптации 13 блока (Передача кода разблокировки функции SWAP)  
   
8. Установка последней прошивки. В случае с приведенным блоком - FL_2Q0907572T_0383___S.odx  

9. Заливка нужной параметрии DA_013_7202_550_T38O_FF_VW376.xml  

10. Проведение кодировок и адаптаций:  

Настройка электроники двигателя
```
Блок 01 → Кодирование
> Байт 5 — Бит 6 → вкл. 
→ Применить (с перезагрузкой блока)
```
Настройка тормозной системы
```
Блок 03 → Кодирование
> Байт 24 — Бит 3 → вкл. 
→ Применить (с перезагрузкой блока)
```
Настройка блока адаптивного круиз-контроля
```
Блок 13 → Кодирование
> Automatic_driveaway_by_pretrigger → activated
> Automatic_driveaway_after_short_stop → activated
> Driveaway_by_triggerleaver → activated
> FPK_functions → installed
→ Применить (с перезагрузкой блока)
```
```
Блок 13 → Адаптации
> Distance_Setting - par_Distance_Setting → on
> Adjustment_mode_time_slot_adaptive_distance_control - Adjustment_mode_time_slot_adaptive_distance_control → on
> Overtaking_right_prevention → deactivated (обгон справа)
> Drive_pmode_selection → MMI_menu_ACC (выбор режима работы в меню ассистентов на магнитоле) 
→ Применить
```
> логин-пароль 14117 

Настройка приборной панели
```
Блок 17 → Кодирование
> adaptive_cruise_control → yes
→ Применить (с перезагрузкой блока)
```
Настройка гейтвея
```
Блок 19 → Кодирование
> FPA_Funktion_ACC → включить
→ Применить (с перезагрузкой блока)
```
```
Блок 19 → Адаптация
> Multi_function_steering_wheel_control_module Coding Value
>> variant → ACC-High
→ Применить
```
Настройка ГУ
```
Блок 5F → Адаптация
> Car_Function_Adaptations_Gen2 - menu_display_ACC → activated
> Car_Function_Adaptations_Gen2 - menu_display_ACC_over_threshold_high → activated
> Car_Function_List_BAP_Gen2 - ACC_0x05 → activated
→ Применить
```
> логин-пароль 20103

Настройка парковочного ассистента
```
Блок 76 → Кодирование
> Adaptive_cruise_control → activated
→ Применить (с перезагрузкой блока)
```
