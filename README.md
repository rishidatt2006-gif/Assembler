# Assembler
REGISTER_MAP = {
    'x0': '00000', 'x1': '00001', 'x2': '00010', 'x3': '00011',
    'x4': '00100', 'x5': '00101', 'x6': '00110', 'x7': '00111',
    'x8': '01000', 'x9': '01001', 'x10': '01010', 'x11': '01011',
    'x12': '01100', 'x13': '01101', 'x14': '01110', 'x15': '01111',
    'x16': '10000', 'x17': '10001', 'x18': '10010', 'x19': '10011',
    'x20': '10100', 'x21': '10101', 'x22': '10110', 'x23': '10111',
    'x24': '11000', 'x25': '11001', 'x26': '11010', 'x27': '11011',
    'x28': '11100', 'x29': '11101', 'x30': '11110', 'x31': '11111',
    'zero': '00000', 'ra': '00001', 'sp': '00010', 'gp': '00011',
    'tp': '00100', 't0': '00101', 't1': '00110', 't2': '00111',
    's0': '01000', 's1': '01001', 'a0': '01010', 'a1': '01011',
    'a2': '01100', 'a3': '01101', 'a4': '01110', 'a5': '01111',
    'a6': '10000', 'a7': '10001', 's2': '10010', 's3': '10011',
    's4': '10100', 's5': '10101', 's6': '10110', 's7': '10111',
    's8': '11000', 's9': '11001', 's10': '11010', 's11': '11011',
    't3': '11100', 't4': '11101', 't5': '11110', 't6': '11111'
}

OPCODES = {
    'add': '0110011', 'addw': '0111011', 'and': '0110011', 'or': '0110011',
    'sll': '0110011', 'slt': '0110011', 'sra': '0110011', 'srl': '0110011',
    'sub': '0110011', 'subw': '0111011', 'xor': '0110011', 'mul': '0110011',
    'mulw': '0111011', 'div': '0110011', 'divw': '0111011', 'rem': '0110011',
    'remw': '0111011',
    'addi': '0010011', 'addiw': '0011011', 'andi': '0010011', 'ori': '0010011',
    'lb': '0000011', 'ld': '0000011', 'lh': '0000011', 'lw': '0000011',
    'jalr': '1100111',
    'sb': '0100011', 'sw': '0100011', 'sh': '0100011', 'sd': '0100011',
    'beq': '1100011', 'bne': '1100011', 'bge': '1100011', 'blt': '1100011',
    'auipc': '0010111', 'lui': '0110111',
    'jal': '1101111'
}

FUNCT3 = {
    'add': '000', 'addw': '000', 'and': '111', 'or': '110',
    'sll': '001', 'slt': '010', 'sra': '101', 'srl': '101',
    'sub': '000', 'subw': '000', 'xor': '100', 'mul': '000',
    'mulw': '000', 'div': '100', 'divw': '100', 'rem': '110',
    'remw': '110',
    'addi': '000', 'addiw': '000', 'andi': '111', 'ori': '110',
    'lb': '000', 'ld': '011', 'lh': '001', 'lw': '010',
    'jalr': '000',
    'sb': '000', 'sw': '010', 'sh': '001', 'sd': '011',
    'beq': '000', 'bne': '001', 'bge': '101', 'blt': '100'
}

FUNCT7 = {
    'add': '0000000', 'addw': '0000000', 'and': '0000000', 'or': '0000000',
    'sll': '0000000', 'slt': '0000000', 'sra': '0100000', 'srl': '0000000',
    'sub': '0100000', 'subw': '0100000', 'xor': '0000000', 'mul': '0000001',
    'mulw': '0000001', 'div': '0000001', 'divw': '0000001', 'rem': '0000001',
    'remw': '0000001'
}

R_FORMATS = [
    'add', 'addw', 'and', 'or', 'sll', 'slt', 'sra', 'srl', 
    'sub', 'subw', 'xor', 'mul', 'mulw', 'div', 'divw', 'rem', 'remw'
]
I_ARITH_FORMATS = ['addi', 'addiw', 'andi', 'ori']
I_LOAD_FORMATS = ['lb', 'ld', 'lh', 'lw']
I_JALR_FORMAT = ['jalr']
S_FORMATS = ['sb', 'sw', 'sh', 'sd']
SB_FORMATS = ['beq', 'bne', 'bge', 'blt']
U_FORMATS = ['auipc', 'lui']
UJ_FORMATS = ['jal']

def read_file(filename):
    try:
        file = open(filename, 'r')
        lines = file.readlines()
        file.close()
        return lines
    except:
        print("Error: Could not read input.asm. Make sure it exists.")
        return []

def write_file(filename, content_list):
    file = open(filename, 'w')
    for line in content_list:
        file.write(line + '\n')
    file.close()

def decimal_to_binary(num_str, length):
    num = int(num_str)
    
    if num >= 0:
        bin_str = bin(num)[2:]
        while len(bin_str) < length:
            bin_str = '0' + bin_str
        
        if len(bin_str) > length:
            bin_str = bin_str[-length:]
        return bin_str
    else:
        num = (1 << length) + num
        bin_str = bin(num)[2:]
        
        if len(bin_str) > length:
             bin_str = bin_str[-length:]
        
        while len(bin_str) < length:
            bin_str = '0' + bin_str
            
        return bin_str

def register_to_binary(reg_str):
    reg = reg_str.replace(',', '')
    if reg in REGISTER_MAP:
        return REGISTER_MAP[reg]
    else:
        return '00000'

def binary_string_to_hex_string(bin_str):
    if not bin_str:
        return "0x00000000"
    
    val = int(bin_str, 2)
    hex_str = hex(val)[2:]
    
    while len(hex_str) < 8:
        hex_str = '0' + hex_str
    
    return '0x' + hex_str

def pass_one(lines):
    symbol_table = {}
    current_address = 0
    current_segment = ".text" 

    for line in lines:
        
        clean_line = line.strip()
        
        if '#' in clean_line:
            clean_line = clean_line.split('#', 1)[0].strip()

        if not clean_line:
            continue

        if clean_line == '.text':
            current_segment = '.text'
            current_address = 0x00000000
            continue
        
        if clean_line == '.data':
            current_segment = '.data'
            current_address = 0x10000000
            continue
        
        if ':' in clean_line:
            parts = clean_line.split(':', 1)
            label = parts[0].strip()
            symbol_table[label] = current_address
            clean_line = parts[1].strip()
            
            if not clean_line:
                continue

        if current_segment == '.text':
            parts = clean_instruction_line(clean_line)
            if not parts:
                continue
            op = parts[0]
            if op == '.globl': 
                continue
            current_address = current_address + 4
        
        if current_segment == '.data':
            
            data_parts = clean_line.split(None, 1) 
            if not data_parts:
                continue
            directive = data_parts[0]
            
            values_str = ""
            if len(data_parts) > 1:
                values_str = data_parts[1]

            values = values_str.replace(',', ' ').split() 
            
            if directive == '.byte':
                current_address = current_address + 1 * len(values)
            elif directive == '.half':
                current_address = current_address + 2 * len(values)
            elif directive == '.word':
                current_address = current_address + 4 * len(values)
            elif directive == '.dword':
                current_address = current_address + 8 * len(values)
            elif directive == '.asciz':
                string_data = values_str.replace('"', '')
                current_address = current_address + len(string_data) + 1
            elif directive == '.globl':
                 pass 

    return symbol_table

def parse_r_format(parts):
    op, rd, rs1, rs2 = parts[0], parts[1], parts[2], parts[3]
    
    opcode = OPCODES[op]
    funct3 = FUNCT3[op]
    funct7 = FUNCT7[op]
    
    rd_bin = register_to_binary(rd)
    rs1_bin = register_to_binary(rs1)
    rs2_bin = register_to_binary(rs2)
    
    binary_code = funct7 + rs2_bin + rs1_bin + funct3 + rd_bin + opcode
    
    binary_fields = opcode + "-" + funct3 + "-" + funct7 + "-" + rd_bin + "-" + rs1_bin + "-" + rs2_bin + "-NULL"
    
    return binary_code, binary_fields

def parse_i_arith_format(parts):
    op, rd, rs1, imm = parts[0], parts[1], parts[2], parts[3]
    
    opcode = OPCODES[op]
    funct3 = FUNCT3[op]
    
    rd_bin = register_to_binary(rd)
    rs1_bin = register_to_binary(rs1)
    imm_bin = decimal_to_binary(imm, 12)
    
    binary_code = imm_bin + rs1_bin + funct3 + rd_bin + opcode
    
    binary_fields = opcode + "-" + funct3 + "-NULL-" + rd_bin + "-" + rs1_bin + "-NULL-" + imm_bin
    
    return binary_code, binary_fields

def parse_i_load_format(parts):
    op, rd, imm, rs1 = parts[0], parts[1], parts[2], parts[3]
    
    opcode = OPCODES[op]
    funct3 = FUNCT3[op]
    
    rd_bin = register_to_binary(rd)
    rs1_bin = register_to_binary(rs1)
    imm_bin = decimal_to_binary(imm, 12)
    
    binary_code = imm_bin + rs1_bin + funct3 + rd_bin + opcode

    binary_fields = opcode + "-" + funct3 + "-NULL-" + rd_bin + "-" + rs1_bin + "-NULL-" + imm_bin

    return binary_code, binary_fields

def parse_i_jalr_format(parts):
    op, rd, rs1, imm = parts[0], parts[1], parts[2], parts[3]

    opcode = OPCODES[op]
    funct3 = FUNCT3[op]

    rd_bin = register_to_binary(rd)
    rs1_bin = register_to_binary(rs1)
    imm_bin = decimal_to_binary(imm, 12)

    binary_code = imm_bin + rs1_bin + funct3 + rd_bin + opcode

    binary_fields = opcode + "-" + funct3 + "-NULL-" + rd_bin + "-" + rs1_bin + "-NULL-" + imm_bin
    
    return binary_code, binary_fields

def parse_s_format(parts):
    op, rs2, imm, rs1 = parts[0], parts[1], parts[2], parts[3]
    
    opcode = OPCODES[op]
    funct3 = FUNCT3[op]

    rs1_bin = register_to_binary(rs1)
    rs2_bin = register_to_binary(rs2)
    
    imm_bin = decimal_to_binary(imm, 12)
    imm_11_5 = imm_bin[0:7]
    imm_4_0 = imm_bin[7:12]
    
    binary_code = imm_11_5 + rs2_bin + rs1_bin + funct3 + imm_4_0 + opcode

    binary_fields = opcode + "-" + funct3 + "-NULL-NULL-" + rs1_bin + "-" + rs2_bin + "-" + imm_bin
    
    return binary_code, binary_fields
    
def parse_sb_format(parts, current_address, symbol_table):
    op, rs1, rs2, label = parts[0], parts[1], parts[2], parts[3]
    
    opcode = OPCODES[op]
    funct3 = FUNCT3[op]
    
    rs1_bin = register_to_binary(rs1)
    rs2_bin = register_to_binary(rs2)
    
    label_address = symbol_table[label]
    offset = label_address - current_address
    
    offset_bin = decimal_to_binary(str(offset), 13)
    
    imm_12 = offset_bin[0]
    imm_11 = offset_bin[1]
    imm_10_5 = offset_bin[2:8]
    imm_4_1 = offset_bin[8:12]

    imm_12_and_10_5 = imm_12 + imm_10_5
    imm_4_1_and_11 = imm_4_1 + imm_11
    
    binary_code = imm_12_and_10_5 + rs2_bin + rs1_bin + funct3 + imm_4_1_and_11 + opcode

    binary_fields = opcode + "-" + funct3 + "-NULL-NULL-" + rs1_bin + "-" + rs2_bin + "-" + offset_bin
    
    return binary_code, binary_fields

def parse_u_format(parts):
    op, rd, imm = parts[0], parts[1], parts[2]
    
    opcode = OPCODES[op]
    rd_bin = register_to_binary(rd)
    
    imm_bin = decimal_to_binary(imm, 20)
    
    binary_code = imm_bin + rd_bin + opcode

    binary_fields = opcode + "-NULL-NULL-" + rd_bin + "-NULL-NULL-" + imm_bin
    
    return binary_code, binary_fields

def parse_uj_format(parts, current_address, symbol_table):
    op, rd, label = parts[0], parts[1], parts[2]
    
    opcode = OPCODES[op]
    rd_bin = register_to_binary(rd)
    
    label_address = symbol_table[label]
    offset = label_address - current_address
    
    offset_bin = decimal_to_binary(str(offset), 21)
    
    imm_20 = offset_bin[0]
    imm_10_1 = offset_bin[10:20]
    imm_11 = offset_bin[9]
    imm_19_12 = offset_bin[1:9]
    
    imm_bin = imm_20 + imm_10_1 + imm_11 + imm_19_12
    
    binary_code = imm_bin + rd_bin + opcode
    
    binary_fields = opcode + "-NULL-NULL-" + rd_bin + "-NULL-NULL-" + offset_bin
    
    return binary_code, binary_fields

def clean_instruction_line(line):
    line = line.replace(',', ' ')
    line = line.replace('(', ' ')
    line = line.replace(')', ' ')
    return line.split()

def pass_two(lines, symbol_table):
    machine_code_lines = []
    current_address = 0
    current_segment = ".text"

    for line in lines:
        clean_line = line.strip()
        
        if '#' in clean_line:
            clean_line = clean_line.split('#', 1)[0].strip()
        
        original_line = clean_line
        
        if not clean_line:
            continue
        
        if clean_line == '.text':
            current_segment = '.text'
            current_address = 0x00000000
            machine_code_lines.append(clean_line)
            continue
            
        if clean_line == '.data':
            current_segment = '.data'
            current_address = 0x10000000
            machine_code_lines.append(clean_line)
            continue
            
        if ':' in clean_line:
            clean_line = clean_line.split(':', 1)[1].strip()
            if not clean_line:
                continue

        if current_segment == '.text':
            parts = clean_instruction_line(clean_line)
            if not parts:
                continue
                
            op = parts[0]
            
            if op == '.globl':
                continue 
            
            binary_code = ""
            binary_fields = ""

            try:
                if op in R_FORMATS:
                    binary_code, binary_fields = parse_r_format(parts)
                elif op in I_ARITH_FORMATS:
                    binary_code, binary_fields = parse_i_arith_format(parts)
                elif op in I_LOAD_FORMATS:
                    binary_code, binary_fields = parse_i_load_format(parts)
                elif op in I_JALR_FORMAT:
                    binary_code, binary_fields = parse_i_jalr_format(parts)
                elif op in S_FORMATS:
                    binary_code, binary_fields = parse_s_format(parts)
                elif op in SB_FORMATS:
                    binary_code, binary_fields = parse_sb_format(parts, current_address, symbol_table)
                elif op in U_FORMATS:
                    binary_code, binary_fields = parse_u_format(parts)
                elif op in UJ_FORMATS:
                    binary_code, binary_fields = parse_uj_format(parts, current_address, symbol_table)
                
                if binary_code:
                    hex_code = binary_string_to_hex_string(binary_code)
                    address_hex = '0x' + hex(current_address)[2:]
                    
                    output_line = address_hex + " " + hex_code + ", " + original_line + " # " + binary_fields
                    machine_code_lines.append(output_line)
                    
                    current_address = current_address + 4
                else:
                    print("Warning: Skipping unsupported instruction or pseudo-instruction: " + line.strip())
            
            except Exception as e:
                print("Error processing line: " + line.strip())
                print(str(e))

        elif current_segment == '.data':
            try:
                data_parts = clean_line.split(None, 1) 
                if not data_parts:
                    continue
                
                directive = data_parts[0]
                
                values_str = ""
                if len(data_parts) > 1:
                    values_str = data_parts[1]

                values = values_str.replace(',', ' ').split() 

                if directive == '.byte':
                    for val in values:
                        address_hex = '0x' + hex(current_address)[2:]
                        hex_val = '0x' + hex(int(val))[2:]
                        while len(hex_val) < 4:
                            hex_val = hex_val.replace('0x', '0x0')
                        machine_code_lines.append(address_hex + " " + hex_val)
                        current_address = current_address + 1
                
                elif directive == '.half':
                    for val in values:
                        address_hex = '0x' + hex(current_address)[2:]
                        hex_val = '0x' + hex(int(val))[2:]
                        while len(hex_val) < 6:
                            hex_val = hex_val.replace('0x', '0x0')
                        machine_code_lines.append(address_hex + " " + hex_val)
                        current_address = current_address + 2

                elif directive == '.word':
                    for val in values:
                        address_hex = '0x' + hex(current_address)[2:]
                        hex_val = '0x' + hex(int(val))[2:]
                        while len(hex_val) < 10:
                            hex_val = hex_val.replace('0x', '0x0')
                        machine_code_lines.append(address_hex + " " + hex_val)
                        current_address = current_address + 4

                elif directive == '.dword':
                    for val in values:
                        address_hex = '0x' + hex(current_address)[2:]
                        hex_val = '0x' + hex(int(val))[2:]
                        while len(hex_val) < 18:
                            hex_val = hex_val.replace('0x', '0x0')
                        machine_code_lines.append(address_hex + " " + hex_val)
                        current_address = current_address + 8
                        
                elif directive == '.asciz':
                    string_data = values_str.replace('"', '')
                    for char in string_data:
                        address_hex = '0x' + hex(current_address)[2:]
                        hex_val = '0x' + hex(ord(char))[2:]
                        machine_code_lines.append(address_hex + " " + hex_val)
                        current_address = current_address + 1
                    
                    address_hex = '0x' + hex(current_address)[2:]
                    hex_val = '0x00'
                    machine_code_lines.append(address_hex + " " + hex_val)
                    current_address = current_address + 1
                
                elif directive == '.globl':
                    pass

            except Exception as e:
                print("Error processing data line: " + line.strip())
                print(str(e))

    return machine_code_lines

def main():
    input_filename = 'input.asm'
    output_filename = 'output.mc'
    
    lines = read_file(input_filename)
    
    if not lines:
        return
        
    symbol_table = pass_one(lines)
    
    output_content = pass_two(lines, symbol_table)
    
    write_file(output_filename, output_content)
    
    print("Assembly complete. Output written to " + output_filename)

main()
