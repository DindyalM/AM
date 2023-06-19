<script>
  import emailjs from 'emailjs-com';
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  const products = writable([
    {
      id: 1,
      img: `https://cheapcanna.org/wp-content/uploads/2023/06/Black-Diamond-AA-cc-700x700.jpg`,
      selected: false,
      description: `$35.00
$70.00
$130.00`,
      name: 'Black Diamond'
    },
    {
      id: 2,
      img: 'https://cheapcanna.org/wp-content/uploads/2023/05/Frosty-Banana-AA-cc-700x700.jpg',
      selected: false,
      description: `$30.00
$70.00
$130.00`,
      name: 'Frosty Banana'
    },
    {
      id: 3,
      img: `https://cheapcanna.org/wp-content/uploads/2023/06/LSD-AA-cc-700x700.jpg`,
      selected: false,
      description: `$30.00
$70.00
$130.00`,
      name: 'LSD'
    },
    {
      id: 4,
      img: `https://cheapcanna.org/wp-content/uploads/2023/06/Romulan-AA-cc-700x700.jpg`,
      selected: false,
      description: `$35.00
$70.00
$130.00`,
      name: 'Romulan'
    }
  ]);

  const toggleSelection = (product) => {
    products.update(allProducts => {
      return allProducts.map(p => p.id === product.id ? { ...p, selected: !p.selected } : p);
    });
  };

  const resetForm = (event, alertMessage) => {
    event.target.reset();
    products.update(allProducts => {
      return allProducts.map(p => ({ ...p, selected: false }));
    });
    alert(alertMessage);
  };

  const generateVerificationCode = () => {
    return Math.floor(100000 + Math.random() * 900000);
  };

  const handleSubmit = async (event) => {
    event.preventDefault();

    const { name, email, phone } = event.target.elements;

      console.log(phone.value);  // Add this line


    let orderedProducts;
    products.subscribe(allProducts => {
      orderedProducts = allProducts.filter((p) => p.selected);
    })();

    if (!orderedProducts.length) {
      alert('Please select at least one product for ordering.');
      return;
    }

    const verificationCode = generateVerificationCode();
    const productPhotoLinks = orderedProducts.map((product) => product.img).join(', ');
    const productDescriptions = orderedProducts.map((product) => product.description).join(', ');

    const templateParams = {
      to_name: 'Slaat',
      from_name: name.value,
      message: verificationCode,
      order: [productDescriptions, productPhotoLinks],
      from_email: email.value,
      from_phone: phone.value
    };

    try {
      await emailjs.send('drw4953', 'template_2ik2luv', templateParams, 'uKJs24_n7AgzyaHKF');
      resetForm(event, 'Form submitted successfully! We will contact you shortly.');
    } catch (error) {
      alert(`An error occurred while submitting the form: ${error.text}`);
    }
  };

  onMount(() => {
    window.scrollTo(0, 0);
  });
</script>

<style>
 /* Reset styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Global styles */
body {
  font-family: Arial, sans-serif;
  background-color: #F2F2F2;
  color: #333;
  line-height: 1.6;
  overflow-x: hidden; /* Hide horizontal scrollbar */
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

/* Header section */
.header-section {
  background-color: #603811;
  padding: 2px 0;
  text-align: center;
}

.header-section h1 {
  font-size: 24px;
  color: #FFF;
  margin: 0;
}

/* Hero section */
.hero-section {
  background-color: #F2E7D4;
  padding: 3px 0;
  text-align: center;
}

.hero-title {
  font-size: 29px;
  color: #603811;
  margin: 0;
}

.hero-subtitle {
  font-size: 16px;
  color: #603811;
  margin: 10px 0 0;
}

/* Image grid */
 .image-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  grid-gap: 10px; /* Adjust gap size according to your preference */

  @media (max-width: 768px) {
    grid-template-columns: repeat(2, 1fr); /* Change to 2 columns on smaller screens */
  }
}

.product-card {
  background-color: #FFF;
  padding: 10px;
  border-radius: 8px;
  text-align: center;
  cursor: pointer;
  transition: background-color 0.3s ease;

  @media (max-width: 768px) {
    flex-basis: calc(50% - 10px); /* Adjust width to 50% minus the gap size */
    margin-right: 0;
    margin-bottom: 10px;
  }
}

.product-card.selected {
  background-color: #F2E7D4;
}

.product-card img {
  width: 120px;
  height: auto;
  border-radius: 8px;
  margin-bottom: 10px;
  object-fit: cover; /* Ensure the images maintain their aspect ratio */
}

.product-card-content {
  margin-top: 10px;
}

.product-card-title {
  font-size: 20px;
  color: #603811;
  margin: 0;
}

.product-card-description {
  font-size: 14px;
  color: #333;
  margin: 10px 0;
}

/* Form section */
.form-section {
  margin-top: 20px;
}

.header {
  font-size: 20px;
  color: #603811;
  margin-bottom: 10px;
  text-align: center;
}

.form-group {
  margin-bottom: 15px;
}

.form-label {
  font-size: 16px;
  color: #603811;
  margin-bottom: 5px;
  display: block;
}

.form-input {
  padding: 8px;
  font-size: 14px;
  border-radius: 4px;
  border: 1px solid #CCC;
  width: 100%;
  box-sizing: border-box;
}

.btn {
  background-color: #603811;
  color: #FFF;
  padding: 8px 16px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-primary {
  background-color: #603811;
}

.btn-primary:hover {
  background-color: #4B290D;
}

/* Instructions section */
.instructions-section {
  margin-top: 30px;
}

.instructions-header {
  font-size: 18px;
  color: #603811;
  margin: 0 0 10px;
  text-align: center;
}

.instructions-content {
  font-size: 14px;
  color: #333;
  margin-bottom: 10px;
  text-align: center;
  line-height: 1.4;
}
</style>

<div class="header-section">
  <h1>Welcome to Amora</h1>
</div>

<div class="hero-section">
  <h2 class="hero-title">Discover the Finest Cannabis Products</h2>
  <p class="hero-subtitle">Browse our selection and place your order</p>
</div>

<div class="instructions-section">
  <h2 class="instructions-header">How to Use the Website</h2>
  <p class="instructions-content">We value your time and will contact you as soon as your form is submitted. Thank you for choosing our services.</p>
</div>


<div class="container">
  <div class="image-grid">
    {#each $products as product}
      <div class="product-card {product.selected ? 'selected' : ''}" on:click={() => toggleSelection(product)}>
        <img src={product.img} alt={product.name} />
        <div class="product-card-content">
          <h2 class="product-card-title">{product.name}</h2>
          <p class="product-card-description">{product.description}</p>
        </div>
      </div>
    {/each}
  </div>

  <div class="form-section">
    <h2 class="header">Submit Details</h2>
    <form class="form" on:submit={handleSubmit}>
      <div class="form-group">
        <label class="form-label" for="name">Full Name:</label>
        <input class="form-input" id="name" name="name" type="text" required>
      </div>
      <div class="form-group">
        <label class="form-label" for="email">Email:</label>
        <input class="form-input" id="email" name="email" type="email" required>
      </div>
      <div class="form-group">
        <label class="form-label" for="phone">Phone:</label>
        <input class="form-input" id="phone" name="phone" type="tel" required>
      </div>
      <button class="btn btn-primary" type="submit">Submit</button>
    </form>
  </div>
</div>
