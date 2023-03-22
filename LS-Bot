import openai_secret_manager

assert "openai" in openai_secret_manager.get_services()
secrets = openai_secret_manager.get_secret("xxxxxxx")

print(secrets) # 如果您得到了一个 JSON 文件，说明您已经成功设置了密钥。

import openai
import re

# 设置 OpenAI API 密钥
openai.api_key = secrets["api_key"]

prompt = (f"您可以使用此机器人与电商商户进行交互，以获取有关他们的信息。"
          f"您可以开始对话，以便机器人可以理解您的意图。"
          f"以下是几个例子：\n"
          f"———\n"
          f"用户：我想知道我的订单何时会到达。\n"
          f"机器人：请问您的订单号是多少？\n"
          f"———\n"
          f"用户：我想知道这个商户有哪些产品。\n"
          f"机器人：请问您对哪种类型的产品感兴趣？\n"
          f"———\n"
          f"用户：我想知道这个商户的联系方式。\n"
          f"机器人：商户的联系方式是：\n"
          f"———\n")


def ask_bot(prompt, max_tokens=50, temperature=0.7):
    """
    向 GPT-3.5 机器人发送 prompt，并返回机器人的回复。
    """
    # 格式化 prompt 并设置引导的最大 token 数量
    prompt = prompt.strip() + "\n———\n用户："
    response = openai.Completion.create(
        engine="text-davinci-002",
        prompt=prompt,
        max_tokens=max_tokens,
        temperature=temperature,
        n=1,
        stop=None,
        frequency_penalty=0,
        presence_penalty=0,
    )

    # 获取机器人的回复
    message = response.choices[0].text.strip()
    # 处理机器人的回复
    # 从机器人的回复中提取订单号、产品类型或商户联系方式等信息。
    order_number = re.search(r"订单号是\s*([^\s,.;!?]+)", message, re.IGNORECASE)
    product_type = re.search(r"感兴趣的产品类型是\s*([^\s,.;!?]+)", message, re.IGNORECASE)
    merchant_contact = re.search(r"商户的联系方式是：?\s*([^\s,.;!?]+)", message, re.IGNORECASE)

    # 根据回复的不同，返回相关的信息。
    if order_number:
        return f"订单 {order_number.group(1)} 预计何时送达？"
    elif product_type:
        return f"商户有以下 {product_type.group(1)} 相关的产品：..."
    elif merchant_contact:
        return f"商户的联系方式为：{merchant_contact.group(1)}"
    else:
        return "很抱歉，我不理解您的请求，请换种说法或提供更多细节。"

while True:
    user_input = input("用户：")
    response = ask_bot(prompt + user_input)
    print("机器人：" + response)




