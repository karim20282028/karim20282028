<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>بوابة الدفع باستخدام Stripe</title>
    <script src="https://js.stripe.com/v3/"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f7f7f7;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .payment-container {
            background-color: white;
            padding: 30px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            border-radius: 10px;
            width: 400px;
            text-align: center;
        }
        h2 {
            color: #333;
        }
        .input-field {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
        }
        .payment-button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 18px;
        }
        .payment-button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="payment-container">
        <h2>دفع باستخدام رقم الهاتف</h2>
        <p>أدخل المبلغ المطلوب للدفع عبر رقم الهاتف:</p>
        <input type="text" class="input-field" id="amount" placeholder="المبلغ" />
        <input type="text" class="input-field" value="01012943983" readonly />
        <button class="payment-button" onclick="startPayment()">دفع الآن</button>
    </div>

    <script>
        // Stripe Configuration
        const stripe = Stripe('YOUR_PUBLISHABLE_KEY');  // ضع مفتاح النشر الخاص بـ Stripe هنا
        const elements = stripe.elements();
        
        function startPayment() {
            const amount = document.getElementById("amount").value;
            if (amount === "" || isNaN(amount)) {
                alert("يرجى إدخال مبلغ صالح");
                return;
            }

            // يتم إرسال المبلغ إلى السيرفر لإنشاء التوكيل الخاص بالدفع
            fetch('/create-payment-intent', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ amount: amount })
            })
            .then(response => response.json())
            .then(data => {
                const clientSecret = data.clientSecret;

                stripe.confirmCardPayment(clientSecret, {
                    payment_method: {
                        card: elements.create('card'),
                        billing_details: {
                            name: 'اسم المستخدم',
                            phone: '01012943983'
                        }
                    }
                }).then(function(result) {
                    if (result.error) {
                        alert('خطأ في عملية الدفع: ' + result.error.message);
                    } else {
                        alert('تمت عملية الدفع بنجاح!');
                    }
                });
            });
        }
    </script>
</body>
</html>
