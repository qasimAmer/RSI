//@version=5
indicator("AO & RSI Combined", shorttitle="AO & RSI", overlay=false)

// --- إعدادات مؤشر Accelerator Oscillator (AO) ---
// إعدادات المدخلات
aoFastLength = input.int(5, title="AO Fast Length", minval=1, group="Accelerator Oscillator")
aoSlowLength = input.int(34, title="AO Slow Length", minval=1, group="Accelerator Oscillator")
aoAccLength = input.int(5, title="AO Accelerator SMA Length", minval=1, group="Accelerator Oscillator")

// القيم الافتراضية لألوان الأشرطة AO
aoColorUp = input.color(color.new(#008000, 0), title="AO Color Up (أخضر)", group="Accelerator Oscillator Colors") // أخضر
aoColorDown = input.color(color.new(#FF0000, 0), title="AO Color Down (أحمر)", group="Accelerator Oscillator Colors") // أحمر

// --- حساب Accelerator Oscillator (AO) ---
ao = ta.sma((high + low) / 2, aoFastLength) - ta.sma((high + low) / 2, aoSlowLength)
aoAccelerator = ao - ta.sma(ao, aoAccLength)

// --- رسم مؤشر Accelerator Oscillator (AO) ---
aoBarColor = color.white
if aoAccelerator > aoAccelerator[1]
    aoBarColor := aoColorUp
else if aoAccelerator < aoAccelerator[1]
    aoBarColor := aoColorDown
else
    aoBarColor := color.gray // في حال عدم وجود تغيير

plot(aoAccelerator, title="AO Accelerator", style=plot.style_columns, color=aoBarColor, linewidth=2)

// رسم مستوى الصفر لـ AO
hline(0, "AO Zero Level", color.gray, linestyle=hline.style_dashed)


// --- إعدادات مؤشر Relative Strength Index (RSI) ---
// فترة RSI
rsiPeriod = input.int(14, title="RSI Period", minval=1, group="Relative Strength Index")

// مصدر السعر (تطبيق على: إغلاق)
rsiSrc = input.source(close, title="RSI Source (Apply to)", group="Relative Strength Index")

// المستويات المخصصة لـ RSI
rsiLevel10 = input.float(10, title="RSI Level 10", group="RSI Levels")
rsiLevel20 = input.float(20, title="RSI Level 20", group="RSI Levels")
rsiLevel45 = input.float(45, title="RSI Level 45", group="RSI Levels")
rsiLevel80 = input.float(80, title="RSI Level 80", group="RSI Levels")
rsiLevel90 = input.float(90, title="RSI Level 90", group="RSI Levels")

// إعدادات الأنماط لخط RSI
rsiLineColor = input.color(color.blue, title="RSI Line Color", group="RSI Styles")
rsiLineWidth = input.int(2, title="RSI Line Width", minval=1, maxval=4, group="RSI Styles")
rsiLineStyleString = input.string("Solid", title="RSI Line Style", options=["Solid", "Dashed", "Dotted"], group="RSI Styles")

// دالة مساعدة لتحويل نمط الخط
getLineStyle(styleString) =>
    if styleString == "Dashed"
        plot.style_line // Changed from line.style_dashed to plot.style_line as plots have styles, not lines directly
    else if styleString == "Dotted"
        plot.style_line // Changed from line.style_dotted
    else
        plot.style_line // Changed from line.style_solid

// --- حساب RSI ---
rsi = ta.rsi(rsiSrc, rsiPeriod)

// --- رسم مؤشر RSI ---
rsiPlot = plot(rsi, title="RSI Line", color=rsiLineColor, linewidth=rsiLineWidth, style=plot.style_line, display=display.all)

// رسم المستويات الأفقية لـ RSI باستخدام plot() بدلاً من hline() لتسهيل التعبئة
// ملاحظة: نستخدم plot(constant_value) هنا لأننا نرسم خطوطًا أفقية ثابتة
p10 = plot(rsiLevel10, title="RSI Level 10", color=color.gray, linewidth=1, style=plot.style_line, display=display.all)
p20 = plot(rsiLevel20, title="RSI Level 20", color=color.gray, linewidth=1, style=plot.style_line, display=display.all)
p45 = plot(rsiLevel45, title="RSI Level 45", color=color.gray, linewidth=1, style=plot.style_line, display=display.all)
p80 = plot(rsiLevel80, title="RSI Level 80", color=color.gray, linewidth=1, style=plot.style_line, display=display.all)
p90 = plot(rsiLevel90, title="RSI Level 90", color=color.gray, linewidth=1, style=plot.style_line, display=display.all)


// تعبئة الخلفية بين مستويات RSI (اختياري)
rsiBandColorOversold = color.new(color.red, 90) // منطقة ذروة البيع (أقل من 20)
rsiBandColorOverbought = color.new(color.green, 90) // منطقة ذروة الشراء (أعلى من 80)

// تصحيح: تمرير معرف الرسم (Plot IDs) لدالة plot إلى دالة fill
fill(p80, p90, color=rsiBandColorOverbought, title="RSI Overbought High Zone")
fill(p20, p10, color=rsiBandColorOversold, title="RSI Oversold High Zone")
