#include <iostream>
#include <cstdlib>
using namespace std;

void printSizes() {
    cout << "bool: " << sizeof(bool) << endl
        << "char: " << sizeof(char) << endl
        << "short int: " << sizeof(short int) << endl
        << "int: " << sizeof(int) << endl
        << "long int: " << sizeof(long int) << endl
        << "long long: " << sizeof(long long) << endl
        << "float: " << sizeof(float) << endl
        << "double: " << sizeof(double) << endl
        << "long double: " << sizeof(long double);
    cout << endl << endl;
}

void printIntBits(int a) {
    int order = sizeof(int) * 8 - 1;
    unsigned mask = 1 << order;

    cout << "[sign]";
    for (int i = 0; i < order + 1; i++) {
        cout << ((a & (mask >> i)) ? '1' : '0');
        if ((order - i) % 8 == 0 && i != order) cout << " ";
    }
    cout << endl;
}

void printFloatBits(float fl) {
    union {
        float f;
        unsigned u;
    } tmp;
    tmp.f = fl;
    unsigned bits = tmp.u;

    int sign = (bits >> 31) & 1;
    int exp = (bits >> 23) & 255;
    int mant = bits & ((1 << 23) - 1);

    cout << "Число: " << fl << endl;
    cout << "[sign]" << sign << " [exp]";
    for (int i = 30; i >= 23; i--) cout << ((bits >> i) & 1);
    cout << " [mant]";
    for (int i = 22; i >= 0; i--) cout << ((bits >> i) & 1);
    cout << endl;
    cout << "Поля: sign=" << sign << ", exp=" << exp
        << " (bias 127, E=" << exp - 127 << "), mant=" << mant << endl;
}

void printDoubleBits(double dbl) {
    union {
        double d;
        unsigned long long u;
    } tmp;
    tmp.d = dbl;
    unsigned long long bits = tmp.u;

    int sign = (bits >> 63) & 1;
    int exp = (bits >> 52) & 2047;
    unsigned long long mant = bits & ((1ULL << 52) - 1);

    cout << "Число: " << dbl << endl;
    cout << "[sign]" << sign << " [exp]";
    for (int i = 62; i >= 52; i--) cout << ((bits >> i) & 1);
    cout << " [mant]";
    for (int i = 51; i >= 0; i--) cout << ((bits >> i) & 1);
    cout << endl;
    cout << "Поля: sign=" << sign << ", exp=" << exp
        << " (bias 1023, E=" << exp - 1023 << "), mant=" << mant << endl;
}

int changeBit(int value, int bit, int op) {
    int mask = 1 << bit;
    if (op == 1) return value | mask;
    if (op == 2) return value & ~mask;
    if (op == 3) return value ^ mask;
    return value;
}

int main() {
    system("chcp 65001 > nul");

    printSizes();

    cout << "2. ЦЕЛЫЕ ЧИСЛА" << endl;
    int num;
    cout << "Введите целое число: ";
    cin >> num;
    cout << "Число: " << num << endl;
    cout << "Двоичное представление: ";
    printIntBits(num);

    unsigned unum;
    cout << "Введите unsigned число: ";
    cin >> unum;
    cout << "Биты unsigned: ";
    printIntBits(unum);
    cout << endl;

    cout << "3. ВЕЩЕСТВЕННЫЕ ЧИСЛА" << endl;
    float fl;
    cout << "Введите число float: ";
    cin >> fl;
    printFloatBits(fl);
    cout << endl;

    double dbl;
    cout << "Введите число double: ";
    cin >> dbl;
    printDoubleBits(dbl);
    cout << endl;

    cout << "4. МАНИПУЛЯЦИЯ БИТАМИ (int)" << endl;
    int x, bit;
    cout << "Введите целое число для изменения бита: ";
    cin >> x;
    cout << "Введите номер бита от 0 до 30: ";
    cin >> bit;

    if (bit < 0 || bit > 30) {
        cout << "Ошибка: номер бита должен быть от 0 до 30." << endl;
    }
    else {
        cout << "Исходное число: " << x << endl;
        cout << "Биты: ";
        printIntBits(x);
        cout << "Установить бит " << bit << " в 1:   " << changeBit(x, bit, 1) << endl;
        cout << "Сбросить бит " << bit << " в 0:    " << changeBit(x, bit, 2) << endl;
        cout << "Инвертировать бит " << bit << ":  " << changeBit(x, bit, 3) << endl;
    }
    cout << endl;

    cout << "4.1. МАНИПУЛЯЦИЯ БИТАМИ (float)" << endl;
    float f_val;
    cout << "Введите число float для смены знака: ";
    cin >> f_val;
    union { float f; unsigned u; } f_conv;
    f_conv.f = f_val;
    f_conv.u ^= (1 << 31);
    cout << "До:   " << f_val << endl;
    cout << "После (сменили бит 31): " << f_conv.f << endl;
    cout << endl;

    cout << "5. ИНДИВИДУАЛЬНОЕ ЗАДАНИЕ" << endl;
    cout << "Задача: Изменить знак числа без умножения на -1." << endl;

    int val;
    cout << "Введите целое число для инверсии знака: ";
    cin >> val;

    cout << "Исходное число: " << val << endl;
    cout << "Биты: ";
    printIntBits(val);

    int inverted = ~val + 1;
    cout << "Число после инверсии знака: " << inverted << endl;
    cout << "Биты: ";
    printIntBits(inverted);

    return 0;
}