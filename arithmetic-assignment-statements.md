# دستورات مقدار دهی حسابی

در یک زبان HDL اغلب می‌توان بطور مستقیم و به سادگی از عملگرهای ریاضی استفاده نمود. Verilog این عملیات را با استفاده از دستورات مقدار دهی حسابی و بردارها پیاده سازی می‌کند. برای مثال، اگر بردار های زیر را در توصیف یک مدار داشته باشیم

```
input [n-1:0] X, Y;
output [n-1:0] S;
```

سپس مقدار دهی حسابی زیر

```
S = X + Y;
```

نشان دهنده‌ی یک جمع کننده‌ی n بیتی است.

علاوه بر عملگر"+"، که برای جمع استفاده می‌شود، در Verilog می‌توان از عملگرهای حسابی دیگر نیز استفاده کرد. کد کامل Verilog که شامل عبارت فوق است در شکل ۱۸ نشان داده شده است. از آنجایی که فقط یک دستور درون بلوک always آمده است، لازم به استفاده از begin و end در ابتدا و انتهای بلوک نمی‌باشد.

![](/assets/pic18.png)**شکل ۱۸**

این کد توصیف کننده‌ی یک مدار جمع کننده‌ی n بیتی است؛ اما شامل سیگنال های بیت خروجی نقلی و بیت سرریز نمی‌باشد. یکی از روش هایی که می‌توان این سیگنال ها را هم در توصیف مدار گنجاند در شکل ۱۹ آمده است. بیت خروجی نقلی که در موقعیت n-1 قرار دارد \(MSB\) را می‌توان به مقادیر Xn-1، Yn-1 و Sn-1 مشتق نمود.

```
�carryout = Xn-1 Yn-1 + Xn-1 S'n-1 + Yn-1 + S'n-1
```

-- توجه داشته باشید که عبارت بالا یک عبارت منطقی است که در آن علامت+نشان دهنده‌ی عمل OR است.

همانطور که می‌دانید برای بدست آوردن بیت سرریز از عبارت زیر استفاده می‌شود.

```
overflow = Cn ⨁ Cn-1
```

در مثال ما \(مدار تمام جمع کننده\)، Cn نشان دهنده‌ی سیگنال carryout می‌باشد. اما نمی‌توان بصورت مستقیم به Cn-1، که مشخص کننده‌ی مکان بیت نقلی n-2 است، دسترسی داشت.می‌توان عبارت Xn-1 ⨁ Yn-1 ⨁ Sn-1 را به عنوان Cn-1 در نظر گرفت. بنابراین داریم:

```
overflow = carryout ⨁ Xn-1 ⨁ Yn-1 ⨁ Sn-1
```

��![](/assets/pic19.png)**شکل ۱۹**

---

یک روش دیگر برای قرار دادن سیگنال های بیت خروجی نقلی و بیت سرریز در شکل ۲۰ نشان داده شده‌است. در این کد از یک بردار n+1 بیتی بنام Sum استفاده شده است. بیت اضافی Sum\[n\]، به بیت خروجی نقلی در مکان n-1 در جمع کننده تبدیل می‌شود. برای حاصل جمع X، Y و carryin که در سیگنال Sum ذخیره می‌شود، از یک عبارت \(syntax\) نامتعارف و غیرمعمول استفاده شده است. عبارات درون براکت، به معنای پیوستن 0 به قسمت چپ بردار n بیتی X و Y است. \(که در این حالت یک بردار n+1 بیتی ایجاد می‌شود\). در Verilog عملگر { , }، عملگر** الحاق ** نامیده می‌شود.

![](/assets/pic20.png)**شکل ۲۰**

---

اگر A یک بردار m بیتی و B یک بردار k بیتی باشد، الحاق {A,B} برابر یک بردار m+k بیتی است که پر ارزش ترین بیت های آن شامل بیت های A \(m بیت\) و کم ارزش ترین بیت های آن، بیت های B \(k بیت\) می‌باشد. در شکل ۲۰ به این دلیل از عملگر الحاق استفاده شده است که Sum\[n\] معادل بیت نقلی مکان n-1 شود.در واقع ما عبارت Xn = Yn = 0 را ایجاد نمودیم که داریم:

```
Sum[n] = 0 + 0 + Cn-1
```

این مثال، مقدمه‌ای ساده بر مفهوم الحاق بود. اما به سادگی میتوان خط کد زیر را هم نوشت

```
Sum = X + Y + carryin;
```

چراکه Sum یک بردار n+1 بیتی است؛ و عملیات جمع در صورتی انجام خواهد شد که X و Y بردارهایی n+1 بیتی باشند که سمت چپ آنها با 0 پر شده است.

عبارت زیر یکی دیگر از عباراتی است که باید در کد شکل ۲۰ به آن توجه نمود.

```
S = Sum[n-1:0];
```

این خط کد n بیت اولSum را به درون S مقداردهی می‌کند؛ و سپس در خط بعد، نقلی خروجی جمع\(Sum\[n\]\) را به سیگنال carryout منتقل می‌کند.

در کد شکل های ۱۹ و ۲۰ برخی از ویژگی هایVerilog برای طراحی جمع کننده نشان داده شد. به طور کلی عمل طراحی یک طرح را می‌توان به روش های متفاوتی انجام داد. در کد شکل ۲۰ از یک بردار n+1 بیتی بنام Sum به عنوان سیگنال واسط برای تولید n بیت از S و خروجی نقلی از طبقه‌ی n-1 جمع کننده، استفاده شده است. این امر مستلزم دو عبارت درVerilogمی‌باشد که بیت های مورد نظر را از Sum استخراج کند. همچنین نحوه‌ی استفاده از عملگر الحاق برای چسباندن 0 به بردارهای X و Y نشان داده شد. اما اشاره شد که این عمل لازم نیست؛ چرا که اگر یک بردار در یک عملیات حسابی بکار گرفته شود که نتیجه‌ای بزرگتر از سایز آن ایجاد کند، آن بردار به طور خودکار با 0 پر می‌شود. می‌توان به نحوی مؤثر تر در سمت چپ عملیات جمع از عملگر الحاق برای چسباندن carryout به بردار S استفاده نمود. به طوری که داریم:

```
{carryout, S} = X + Y + carryin;
```

بنابراین دیگر نیازی به سیگنال Sum نمی‌باشد و کدVerilog به سادگی شکل ۲۱ می‌شود. از آنجایی که هر دو شکل ۲۰ و ۲۱ یک رفتار مشابه از جمع کننده را توصیف می‌کنند، کامپایلرVerilog مدار مشابهی را برای هر دو کد تولید می‌نماید. کد شکل ۲۱ ساده تر و ظریف تر است.

![](/assets/pic21.png)**شکل ۲۱**

برای توصیف یک مدار جمع کننده می توان از روش شکل ۲۲ استفاده نمود. بر خلاف کد های قبلی که در آن ها ساختار کامل مدار تمام جمع کننده بر اساس عملگرهای منطقی تعریف شده بود، در این حالت، کد دستورات فقط رفتار خود مدار را توصیف می‌کنند و کامپایلرVerilog به پیاده سازی جزئیات مناسب با توجه به تکنولوژی استفاده شده مورد نظر می‌پردازد.

![](/assets/pic22.png)**شکل ۲۲**

