from flask import Flask, render_template, request, redirect, url_for, session, flash
from flask_sqlalchemy import SQLAlchemy
import os

app = Flask(__name__)
app.secret_key = 'your_secret_key'

# Database setup
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///ecommerce.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

# Models
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(150), unique=True, nullable=False)
    password = db.Column(db.String(150), nullable=False)
    
class Product(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text, nullable=False)
    price = db.Column(db.Float, nullable=False)
    image_url = db.Column(db.String(200), nullable=False)
    
class CartItem(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    product_id = db.Column(db.Integer, db.ForeignKey('product.id'), nullable=False)
    quantity = db.Column(db.Integer, default=1)

# Utility functions
def calculate_total(cart_items):
    total = 0
    for item in cart_items:
        product = Product.query.get(item.product_id)
        total += product.price * item.quantity
    return total

# Routes
@app.route('/')
def home():
    products = Product.query.all()
    return render_template('index.html', products=products, title="Home")

@app.route('/product/<int:product_id>')
def product_page(product_id):
    product = Product.query.get_or_404(product_id)
    return render_template('product.html', product=product, title=product.name)

@app.route('/cart')
def cart():
    cart_items = CartItem.query.all()
    total = calculate_total(cart_items)
    return render_template('cart.html', cart_items=cart_items, total=total, title="Shopping Cart")

@app.route('/add_to_cart/<int:product_id>')
def add_to_cart(product_id):
    cart_item = CartItem(product_id=product_id)
    db.session.add(cart_item)
    db.session.commit()
    flash("Product added to cart!", "success")
    return redirect(url_for('cart'))

@app.route('/remove_from_cart/<int:cart_item_id>')
def remove_from_cart(cart_item_id):
    cart_item = CartItem.query.get_or_404(cart_item_id)
    db.session.delete(cart_item)
    db.session.commit()
    flash("Product removed from cart!", "success")
    return redirect(url_for('cart'))

@app.route('/checkout', methods=['GET', 'POST'])
def checkout():
    if request.method == 'POST':
        # Simulate payment processing and order confirmation
        flash("Order placed successfully!", "success")
        return redirect(url_for('home'))
    return render_template('checkout.html', title="Checkout")

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        name = request.form['name']
        email = request.form['email']
        password = request.form['password']
        user = User(name=name, email=email, password=password)
        db.session.add(user)
        db.session.commit()
        flash("Registration successful!", "success")
        return redirect(url_for('login'))
    return render_template('register.html', title="Register")

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        email = request.form['email']
        password = request.form['password']
        user = User.query.filter_by(email=email, password=password).first()
        if user:
            session['user_id'] = user.id
            flash("Login successful!", "success")
            return redirect(url_for('home'))
        else:
            flash("Invalid credentials", "danger")
    return render_template('login.html', title="Login")

@app.route('/logout')
def logout():
    session.pop('user_id', None)
    flash("Logged out successfully!", "success")
    return redirect(url_for('home'))

# SEO helpers
@app.context_processor
def inject_meta_tags():
    return {
        "meta_description": "Welcome to our e-commerce website, featuring high-quality products and secure checkout.",
        "meta_keywords": "e-commerce, shopping, products, online store"
    }

if __name__ == '__main__':
    if not os.path.exists('ecommerce.db'):
        with app.app_context():
            db.create_all()
    app.run(debug=False)
