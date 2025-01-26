const productsData = {
        "ROOTT R": [
            "R3010", "R3012", "R3014", "R3016", "R3506", "R3508", "R3510", "R3512", "R3514", "R3516", "R3806", "R3808", "R3810", "R3812", "R3814", "R3816", "R4206", "R4208", "R4210", "R4212", "R4214", "R4216", "R4806", "R4808", "R4810", "R4812", "R4814", "R4816", "R5506", "R5508", "R5510", "R5512", "R5514", "R5516"
        ],
        "ROOTT C COMPRESSIVE": [
            "C3006", "C3008", "C3010", "C3012", "C3014", "C3016", "C3506", "C3508", "C3510", "C3512", "C3514", "C3516", "C4006", "C4008", "C4010", "C4012", "C4014", "C4016", "C4506", "C4508", "C4510", "C4512", "C4514", "C4516", "C5006", "C5008", "C5010", "C5012", "C5014", "C5016", "C5506", "C5508", "C5510", "C5512", "C5514", "C5516"
        ],
        "ROOTT M": [
            "C3006M", "C3008M", "C3010M", "C3012M", "C3014M", "C3016M", "C3506M", "C3508M", "C3510M", "C3512M", "C3514M", "C3516M", "C4006M", "C4008M", "C4010M", "C4012M", "C4014M", "C4016M", "C4506M", "C4508M", "C4510M", "C4512M", "C4514M", "C4516M", "C5006M", "C5008M", "C5010M", "C5012M", "C5014M", "C5016M", "C5506M", "C5508M", "C5510M", "C5512M", "C5514M", "C5516M"
        ]
    };

    let orderItemCount = 0;

   function generateWhatsappLink() {
    let orderItems = [];
    let notes = document.getElementById('order-notes').value;
    let name = document.getElementById('customer-name').value;
    let email = document.getElementById('customer-email').value;
    let address = document.getElementById('customer-address').value;

    // التحقق من المدخلات
    if (name.trim() === '') {
        alert('الرجاء إدخال اسم العميل');
        return;
    }
    if (email.trim() === '' || !validateEmail(email)) {
        alert('الرجاء إدخال بريد إلكتروني صحيح');
        return;
    }
    if (address.trim() === '') {
        alert('الرجاء إدخال العنوان');
        return;
    }

    const orderItemsContainer = document.getElementById('order-items');
    const orderItemDivs = orderItemsContainer.getElementsByClassName('order-item');
    if (orderItemDivs.length === 0) {
        alert('الرجاء إضافة منتج واحد على الأقل');
        return;
    }

    let tableHeader = `Product | Size | Quantity\n`;
    tableHeader += `-------------------------------------\n`;
    orderItems.push(tableHeader);

    let productValid = true;
    let quantityValid = true;

    for (let itemDiv of orderItemDivs) {
        const productSelect = itemDiv.querySelector('select[name^="product-"]');
        const sizeSelect = itemDiv.querySelector('select[name^="size-"]');
        const quantityInput = itemDiv.querySelector('input[name^="quantity-"]');

        const productName = productSelect.options[productSelect.selectedIndex].value;
        const sizeName = sizeSelect.options[sizeSelect.selectedIndex].value;
        const quantity = quantityInput.value;

        // التحقق من المنتج
        if (productName === '') {
            productValid = false;
            break;
        }

        // التحقق من الحجم
        if (sizeName === '') {
            productValid = false;
            break;
        }

        // التحقق من الكمية
        if (quantity === '' || parseInt(quantity) <= 0) {
            quantityValid = false;
            break;
        }

        orderItems.push(`${productName} | ${sizeName} | ${quantity}\n`);
    }

    if (!productValid) {
        alert('الرجاء اختيار منتج وحجم لكل عنصر');
        return;
    }

    if (!quantityValid) {
        alert('الرجاء إدخال كمية صحيحة أكبر من صفر لكل منتج');
        return;
    }

    let message = encodeURIComponent(`الاسم: ${name}\nالبريد الإلكتروني: ${email}\nالعنوان: ${address}\n\n${orderItems.join('')}`);
    message += `\nملاحظات: ${encodeURIComponent(notes)}`;

    const whatsappLink = `https://wa.me/201099331401?text=${message}`;
    window.open(whatsappLink, "_blank");
}

// التحقق من صحة البريد الإلكتروني
function validateEmail(email) {
    const regex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;
    return regex.test(email);
}




    function addProduct() {
        orderItemCount++;

        const orderItemsContainer = document.getElementById('order-items');
        
        const orderItemDiv = document.createElement('div');
        orderItemDiv.classList.add('order-item');
        
        const productSelect = document.createElement('select');
        productSelect.name = `product-${orderItemCount}`;
        const sizeSelect = document.createElement('select');
        sizeSelect.name = `size-${orderItemCount}`;
        const quantityInput = document.createElement('input');
        quantityInput.name = `quantity-${orderItemCount}`;
        quantityInput.type = 'number';
        quantityInput.placeholder = 'العدد';

        // Add product options dynamically
        Object.keys(productsData).forEach(product => {
            const option = document.createElement('option');
            option.value = product;
            option.innerText = product;
            productSelect.appendChild(option);
        });

        // Add sizes options dynamically based on product selected
        productSelect.addEventListener('change', function() {
            const selectedProduct = this.value;
            const sizes = productsData[selectedProduct];
            sizeSelect.innerHTML = ''; // Clear existing sizes
            sizes.forEach(size => {
                const sizeOption = document.createElement('option');
                sizeOption.value = size;
                sizeOption.innerText = size;
                sizeSelect.appendChild(sizeOption);
            });
        });

        // Trigger initial size load
        productSelect.dispatchEvent(new Event('change'));

        const removeButton = document.createElement('span');
        removeButton.classList.add('remove-item');
        removeButton.innerText = 'إزالة';
        removeButton.onclick = function() {
            orderItemDiv.remove();
        };

        orderItemDiv.appendChild(productSelect);
        orderItemDiv.appendChild(sizeSelect);
        orderItemDiv.appendChild(quantityInput);
        orderItemDiv.appendChild(removeButton);

        orderItemsContainer.appendChild(orderItemDiv);
    }

    function openOrderForm() {
        document.getElementById('order-form').style.display = 'block';
    }

    function changeLanguage(language) {
        const elementsToTranslate = {
            "ar": {
                "site-title": "Roott Implant System",
                "branch-title": "فرع إسكندرية",
        "branch-address": "العصافرة بحري، شارع سليمان الفارسي، بجوار جزارة الحمد",
                "products-title": "المنتجات",
                "order-title": "لطلب الأوردر، اضغط هنا:",
                "order-button": "طلب أوردر",
                "order-form-title": "اختر المنتجات والمقاسات والكميات التي ترغب في طلبها:",
                "add-product-button": "إضافة منتج",
                "note-label": "ملاحظات:",
                "name-label": "الاسم:",
                "address-label": "العنوان:",
                "submit-button": "إرسال الطلب",
                "footer-text": "حقوق النشر © 2025 Roott Implant System"
            },
            "en": {
                "site-title": "Roott Implant System",
                "branch-title": "Alexandria Branch",
        "branch-address": "Al-Asafra Bahari, Sulaiman Al-Farsi Street, Next to Al-Hamd Butchery",
                "products-title": "Products",
                "order-title": "To place an order, click here:",
                "order-button": "Order Now",
                "order-form-title": "Choose the products, sizes, and quantities you wish to order:",
                "add-product-button": "Add Product",
                "note-label": "Notes:",
                "name-label": "Name:",
                "address-label": "Address:",
                "submit-button": "Submit Order",
                "footer-text": "Copyright © 2025 Roott Implant System"
            }
        };

        const translations = elementsToTranslate[language];
        for (const [id, translation] of Object.entries(translations)) {
            document.getElementById(id).innerText = translation;
        }
    }
<script src="script.js"></script>
</body>
</html>
