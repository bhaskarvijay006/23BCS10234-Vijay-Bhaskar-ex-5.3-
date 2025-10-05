# 23BCS10234-Vijay-Bhaskar-ex-5.3-

const express = require('express');
const mongoose = require('mongoose');
const app = express();
app.use(express.json());

mongoose.connect('mongodb://localhost:27017/ecommerce', {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

const VariantSchema = new mongoose.Schema({
  color: String,
  size: String,
  stock: Number
});

const ProductSchema = new mongoose.Schema({
  name: String,
  price: Number,
  category: String,
  variants: [VariantSchema]
});

const StudentSchema = new mongoose.Schema({
  name: String,
  age: Number,
  course: String
});

const Product = mongoose.model('Product', ProductSchema);
const Student = mongoose.model('Student', StudentSchema);

app.post('/init', async (req, res) => {
  await Product.deleteMany({});
  await Product.insertMany([
    {
      name: "Running Shoes",
      price: 120,
      category: "Footwear",
      variants: [
        { color: "Red", size: "M", stock: 10 },
        { color: "Blue", size: "L", stock: 5 }
      ]
    },
    {
      name: "Smartphone",
      price: 699,
      category: "Electronics",
      variants: []
    }
  ]);
  res.send({ message: "Sample products inserted" });
});

app.get('/products', async (req, res) => {
  const products = await Product.find();
  res.json(products);
});

app.get('/products/category/:category', async (req, res) => {
  const category = req.params.category;
  const products = await Product.find({ category });
  res.json(products);
});

app.get('/products/by-color/:color', async (req, res) => {
  const color = req.params.color;
  const products = await Product.find({ "variants.color": color });
  res.json(products);
});

app.delete('/students/:id', async (req, res) => {
  const student = await Student.findByIdAndDelete(req.params.id);
  if (student) {
    res.json({
      message: "Student deleted",
      student
    });
  } else {
    res.status(404).json({ message: "Student not found" });
  }
});

app.listen(3000, () => {
  console.log(`Server running on http://localhost:3000`);
});
