#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <fstream>

using namespace std;

// Структура для хранения информации о товаре
struct Product {
    int id;
    string name;
    double price;
};

// Структура для хранения информации о клиенте
struct Customer {
    int id;
    string name;
    string address;
    string phone;
};

// Структура для хранения информации о заказе
struct Order {
    int id;
    int customer_id;
    vector<pair<int, int>> items; // {product_id, quantity}
    double total_price;
};

// Вектор для хранения всех товаров
vector<Product> products;

// Вектор для хранения всех клиентов
vector<Customer> customers;

// Вектор для хранения всех заказов
vector<Order> orders;

// Генерация уникального ID для товаров, клиентов и заказов
int generateID(vector<Product>& products) {
    if (products.empty()) {
        return 1;
    }
    return products.back().id + 1;
}

int generateID(vector<Customer>& customers) {
    if (customers.empty()) {
        return 1;
    }
    return customers.back().id + 1;
}

int generateID(vector<Order>& orders) {
    if (orders.empty()) {
        return 1;
    }
    return orders.back().id + 1;
}

// Функция добавления нового товара
void addProduct() {
    Product product;
    product.id = generateID(products);
    cout << "Введите название товара: ";
    cin >> product.name;
    cout << "Введите цену товара: ";
    cin >> product.price;
    products.push_back(product);
    cout << "Товар добавлен успешно!" << endl;
}

// Функция добавления нового клиента
void addCustomer() {
    Customer customer;
    customer.id = generateID(customers);
    cout << "Введите имя клиента: ";
    cin >> customer.name;
    cout << "Введите адрес клиента: ";
    cin >> customer.address;
    cout << "Введите телефон клиента: ";
    cin >> customer.phone;
    customers.push_back(customer);
    cout << "Клиент добавлен успешно!" << endl;
}

// Функция создания нового заказа
void createOrder() {
    Order order;
    order.id = generateID(orders);
    cout << "Введите ID клиента: ";
    int customer_id;
    cin >> customer_id;
    order.customer_id = customer_id;

    char addMore = 'y';
    while (addMore == 'y') {
        int product_id;
        int quantity;
        cout << "Введите ID товара: ";
        cin >> product_id;
        cout << "Введите количество: ";
        cin >> quantity;
        order.items.push_back(make_pair(product_id, quantity));
        cout << "Добавить еще товар (y/n)? ";
        cin >> addMore;
    }

    // Рассчет общей стоимости заказа
    order.total_price = 0;
    for (auto item : order.items) {
        for (auto product : products) {
            if (product.id == item.first) {
                order.total_price += product.price * item.second;
                break;
            }
        }
    }

    orders.push_back(order);
    cout << "Заказ создан успешно!" << endl;
}

// Функция вывода списка товаров
void showProducts() {
    cout << "Список товаров:" << endl;
    cout << "ID\tНазвание\tЦена" << endl;
    for (auto product : products) {
        cout << product.id << "\t" << product.name << "\t" << product.price << endl;
    }
}

// Функция вывода списка клиентов
void showCustomers() {
    cout << "Список клиентов:" << endl;
    cout << "ID\tИмя\tАдрес\tТелефон" << endl;
    for (auto customer : customers) {
        cout << customer.id << "\t" << customer.name << "\t" << customer.address << "\t" << customer.phone << endl;
    }
}

// Функция вывода списка заказов
void showOrders() {
    cout << "Список заказов:" << endl;
    cout << "ID\tКлиент\tТовары\tОбщая стоимость" << endl;
    for (auto order : orders) {
        cout << order.id << "\t";
        for (auto customer : customers) {
            if (customer.id == order.customer_id) {
                cout << customer.name << "\t";
                break;
            }
        }
        cout << "{";
        for (size_t i = 0; i < order.items.size(); i++) {
            auto item = order.items[i];
            for (auto product : products) {
                if (product.id == item.first) {
                    cout << product.name << ":" << item.second;
                    if (i < order.items.size() - 1) {
                        cout << ", ";
                    }
                    break;
                }
            }
        }
        cout << "}\t" << order.total_price << endl;
    }
}

// Сохранение данных в файл
void saveData() {
    ofstream outfile("products.txt");
    for (auto product : products) {
        outfile << product.id << "," << product.name << "," << product.price << endl;
    }
    outfile.close();

    outfile.open("customers.txt");
    for (auto customer : customers) {
        outfile << customer.id << "," << customer.name << "," << customer.address << "," << customer.phone << endl;
    }
    outfile.close();

    outfile.open("orders.txt");
    for (auto order : orders) {
        outfile << order.id << "," << order.customer_id << ",";
        for (size_t i = 0; i < order.items.size(); i++) {
            auto item = order.items[i];
            outfile << item.first << ":" << item.second;
            if (i < order.items.size() - 1) {
                outfile << ",";
            }
        }
        outfile << "," << order.total_price << endl;
    }
    outfile.close();
}

// Загрузка данных из файла
void loadData() {
    ifstream infile("products.txt");
    string line;
    while (getline(infile, line)) {
        string idStr, name, priceStr;
        size_t pos = line.find(',');
        idStr = line.substr(0, pos);
        line = line.substr(pos + 1);
        pos = line.find(',');
        name = line.substr(0, pos);
        priceStr = line.substr(pos + 1);
        products.push_back({stoi(idStr), name, stod(priceStr)});
    }
    infile.close();

    infile.open("customers.txt");
    while (getline(infile, line)) {
        string idStr, name, address, phone;
        size_t pos = line.find(',');
        idStr = line.substr(0, pos);
        line = line.substr(pos + 1);
        pos = line.find(',');
        name = line.substr(0, pos);
        line = line.substr(pos + 1);
        pos = line.find(',');
        address = line.substr(0, pos);
        phone = line.substr(pos + 1);
        customers.push_back({stoi(idStr), name, address, phone});
    }
    infile.close();

    infile.open("orders.txt");
    while (getline(infile, line)) {
        string idStr, customerIDStr, itemsStr, totalPriceStr;
        size_t pos = line.find(',');
        idStr = line.substr(0, pos);
        line = line.substr(pos + 1);
        pos = line.find(',');
        customerIDStr = line.substr(0, pos);
        line = line.substr(pos + 1);
        pos = line.find(',');
        itemsStr = line.substr(0, pos);
        totalPriceStr = line.substr(pos + 1);

        vector<pair<int, int>> items;
        if (itemsStr != "") {
            size_t start = 0;
            size_t end = itemsStr.find(',');
            while (end != string::npos) {
                string itemStr = itemsStr.substr(start, end - start);
                size_t colonPos = itemStr.find(':');
                items.push_back({stoi(itemStr.substr(0, colonPos)), stoi(itemStr.substr(colonPos + 1))});
                start = end + 1;
                end = itemsStr.find(',', start);
            }
            if (start < itemsStr.size()) {
                string itemStr = itemsStr.substr(start);
                size_t colonPos = itemStr.find(':');
                items.push_back({stoi(itemStr.substr(0, colonPos)), stoi(itemStr.substr(colonPos + 1))});
            }
        }

        orders.push_back({stoi(idStr), stoi(customerIDStr), items, stod(totalPriceStr)});
    }
    infile.close();
}

int main() {
    loadData(); // Загрузка данных из файла

    int choice;
    while (true) {
        cout << "Меню:" << endl;
        cout << "1. Добавить товар" << endl;
        cout << "2. Добавить клиента" << endl;
        cout << "3. Создать заказ" << endl;
        cout << "4. Показать товары" << endl;
        cout << "5. Показать клиентов" << endl;
        cout << "6. Показать заказы" << endl;
        cout << "7. Сохранить данные" << endl;
        cout << "0. Выйти" << endl;
        cout << "Выберите действие: ";
        cin >> choice;

        switch (choice) {
            case 1:
                addProduct();
                break;
            case 2:
                addCustomer();
                break;
            case 3:
                createOrder();
                break;
            case 4:
                showProducts();
                break;
            case 5:
                showCustomers();
                break;
            case 6:
                showOrders();
                break;
            case 7:
                saveData();
                break;
            case 0:
                cout << "До свидания!" << endl;
                return 0;
            default:
                cout << "Некорректный выбор. Попробуйте еще раз." << endl;
        }
    }
}
