# PM2_E-Tire-Kar-Management
 
Schema 

CREATE TABLE Roles (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) UNIQUE,
    description TEXT,
    access_level INT
);

CREATE TABLE Employee (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    employee_code VARCHAR(20) UNIQUE,
    firstName VARCHAR(50),
    lastName VARCHAR(50),
    email VARCHAR(100) UNIQUE,
    phone VARCHAR(20),
    role_id BIGINT,
    status ENUM('active', 'on-leave', 'terminated'),
    FOREIGN KEY (role_id) REFERENCES Roles(id),
    INDEX (role_id)
);

CREATE TABLE Inventory (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    vehicleType VARCHAR(50),
    itemType VARCHAR(50),
    brand VARCHAR(100),
    quantity INT,
    price DECIMAL(10,2),
    cost DECIMAL(10,2),
    last_updated_by BIGINT,
    status ENUM('active', 'low', 'discontinued'),
    FOREIGN KEY (last_updated_by) REFERENCES Employee(id),
    INDEX (quantity)
);

CREATE TABLE Supplier (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    contact_person VARCHAR(50),
    phone VARCHAR(20),
    email VARCHAR(100),
    address VARCHAR(200),
    account_number VARCHAR(30),
    status ENUM('active', 'inactive')
);

CREATE TABLE Expenses (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    category VARCHAR(50),
    amount DECIMAL(12,2),
    transaction_date DATE,
    approved_by BIGINT,
    FOREIGN KEY (approved_by) REFERENCES Employee(id),
    INDEX (category),
    INDEX (transaction_date)
);

CREATE TABLE ServiceLog (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    service_id BIGINT,
    payment_id BIGINT NULL,
    timestamp DATETIME,
    activity_type VARCHAR(50),
    notes TEXT,
    parts_used TEXT,
    employee_id INT,
    vehicle_details VARCHAR(100),
    FOREIGN KEY (employee_id) REFERENCES Employee(id),
    INDEX (service_id),
    INDEX (payment_id),
    INDEX (timestamp)
);

CREATE TABLE AuditLog (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    table_name VARCHAR(50),
    record_id INT,
    field_name VARCHAR(50),
    old_value TEXT,
    new_value TEXT,
    action_type ENUM('INSERT', 'UPDATE', 'DELETE'),
    timestamp DATETIME,
    employee_id INT NULL,
    FOREIGN KEY (employee_id) REFERENCES Employee(id)
);

-- Customer Operations Domain

CREATE TABLE Customer (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    customer_number VARCHAR(20) UNIQUE,
    firstName VARCHAR(50),
    lastName VARCHAR(50),
    contact VARCHAR(20),
    loyalty_points INT,
    INDEX (contact)
);

CREATE TABLE Sale (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    invoice_number VARCHAR(50),
    sale_date DATE,
    subtotal DECIMAL(12,2),
    tax_amount DECIMAL(10,2),
    total_amount DECIMAL(12,2),
    employee_id BIGINT,
    customer_id BIGINT,
    FOREIGN KEY (employee_id) REFERENCES Employee(id),
    FOREIGN KEY (customer_id) REFERENCES Customer(id),
    INDEX (invoice_number),
    INDEX (sale_date),
    INDEX (employee_id),
    INDEX (customer_id)
);

CREATE TABLE SaleItem (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    sale_id BIGINT,
    inventory_id BIGINT,
    quantity INT,
    unit_price DECIMAL(10,2),
    line_total DECIMAL(10,2),
    FOREIGN KEY (sale_id) REFERENCES Sale(id),
    FOREIGN KEY (inventory_id) REFERENCES Inventory(id),
    INDEX (sale_id),
    INDEX (inventory_id)
);

CREATE TABLE Services (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    description TEXT,
    price DECIMAL(10,2),
    scheduled_date TIMESTAMP,
    assigned_employee_id BIGINT,
    customer_id BIGINT,
    status ENUM('scheduled', 'in-progress', 'completed'),
    FOREIGN KEY (assigned_employee_id) REFERENCES Employee(id),
    FOREIGN KEY (customer_id) REFERENCES Customer(id),
    INDEX (scheduled_date),
    INDEX (assigned_employee_id),
    INDEX (customer_id)
);

CREATE TABLE Payment (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    sale_id BIGINT,
    services_id BIGINT,
    customer_id BIGINT,
    transaction_code VARCHAR(50),
    payment_date DATETIME,
    payment_method ENUM('Cash', 'Credit Card', 'Check', 'Online'),
    amount DECIMAL(10,2),
    reference_number VARCHAR(50),
    status ENUM('Completed', 'Pending', 'Failed'),
    employee_id INT,
    FOREIGN KEY (sale_id) REFERENCES Sale(id),
    FOREIGN KEY (services_id) REFERENCES Services(id),
    FOREIGN KEY (customer_id) REFERENCES Customer(id),
    FOREIGN KEY (employee_id) REFERENCES Employee(id),
    INDEX (sale_id),
    INDEX (services_id),
    INDEX (customer_id)
);

-- Financial Domain

CREATE TABLE Profit (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    period_start_date DATE,
    period_end_date DATE,
    total_revenue DECIMAL(14,2),
    total_expenses DECIMAL(14,2),
    net_profit DECIMAL(14,2),
    INDEX (period_start_date),
    INDEX (period_end_date)
);

CREATE TABLE MonthlySummary (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    month DATE,
    total_sales DECIMAL(14,2),
    total_services DECIMAL(14,2),
    total_expenses DECIMAL(14,2),
    customer_count INT,
    top_employee_id BIGINT,
    FOREIGN KEY (top_employee_id) REFERENCES Employee(id),
    INDEX (month)
);

CREATE TABLE EmployeePerformance (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    employee_id BIGINT,
    period_start_date DATE,
    period_end_date DATE,
    total_sales DECIMAL(12,2),
    total_services DECIMAL(12,2),
    performance_rating DECIMAL(3,1),
    FOREIGN KEY (employee_id) REFERENCES Employee(id),
    INDEX (employee_id),
    INDEX (period_start_date),
    INDEX (period_end_date)
);